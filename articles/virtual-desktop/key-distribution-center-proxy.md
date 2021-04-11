---
title: Konfigurera Kerberos Key Distribution Center proxy Windows Virtual Desktop – Azure
description: Konfigurera en Windows-pool för virtuella skriv bord för att använda en Kerberos Key Distribution Center proxy.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a1e6d2f4fc0444be364ce3c07c14f6408b3f19fe
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446918"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Konfigurera en Kerberos Key Distribution Center proxy (förhands granskning)

> [!IMPORTANT]
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.
> Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Säkerhets medveten kunder, till exempel finansiella eller myndighets organisationer, loggar ofta in med smartkort. Smartkort gör distributioner säkrare genom att kräva multifaktorautentisering (MFA). För RDP-delen av en Windows Virtual Desktop-Session kräver dock smartkort en direkt anslutning, eller "sikt", med en Active Directory (AD) domänkontrollant för Kerberos-autentisering. Utan denna direkta anslutning kan användarna inte automatiskt logga in till organisationens nätverk från fjärr anslutningar. Användare i en Windows-distribution med virtuella skriv bord kan använda KDC-proxyservern för att använda proxyn för autentisering och logga in på distans. KDC-proxyn gör det möjligt att autentisera Remote Desktop Protocol av en Windows Virtual Desktop-Session, vilket gör att användaren kan logga in på ett säkert sätt. Detta gör att det blir mycket enklare att arbeta från hemmet och att vissa katastrof återställnings scenarier kan köras smidigt.

Att konfigurera KDC-proxyn innebär dock vanligt vis att tilldela Windows Server Gateway-rollen i Windows Server 2016 eller senare. Hur använder du en Fjärrskrivbordstjänster roll för att logga in på Windows Virtual Desktop? Vi tar en titt på komponenterna för att få svar på det.

Det finns två komponenter till den Windows Virtual Desktop-tjänst som behöver autentiseras:

- Feeden i den virtuella Windows-klienten som ger användare en lista över tillgängliga Skriv bord eller program som de har åtkomst till. Den här autentiseringsprocessen sker i Azure Active Directory, vilket innebär att den här komponenten inte fokuserar på den här artikeln.
- RDP-sessionen som resulterar i en användare som väljer någon av de tillgängliga resurserna. Den här komponenten använder Kerberos-autentisering och kräver en KDC-proxy för fjärran vändare.

Den här artikeln visar hur du konfigurerar feeden i Windows Virtual Desktop-klienten i Azure Portal. Om du vill lära dig hur du konfigurerar RD Gateway-rollen, se [distribuera RD Gateway-rollen](/windows-server/remote/rd-gateway-role).

## <a name="requirements"></a>Krav

Om du vill konfigurera en Windows-värd för fjärrskrivbordssession med en KDC-proxy behöver du följande saker:

- Åtkomst till Azure Portal och ett Azure-administratörskonto.
- Fjärrklientdatorerna måste köra antingen Windows 10 eller Windows 7 och ha [Windows Desktop-klienten](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) installerad.
- Du måste ha en KDC-proxy redan installerad på datorn. Information om hur du gör finns i [Konfigurera RD Gateway-rollen för Windows Virtual Desktop](rd-gateway-role.md).
- Datorns operativ system måste vara Windows Server 2016 eller senare.

När du har bestämt dig för att uppfylla de här kraven är du redo att komma igång.

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

6. Den valda poolen ska nu börja utfärda RDP-anslutningsfiler som innehåller det kdcproxyname-värde som du angav i steg 4.

## <a name="next-steps"></a>Nästa steg

Information om hur du hanterar den Fjärrskrivbordstjänster sidan av KDC-proxyn och tilldelar RD Gateway-rollen finns i [distribuera RD Gateway-rollen](rd-gateway-role.md).

Om du är intresse rad av att skala KDC-proxyservrarna lär du dig att konfigurera hög tillgänglighet för KDC-proxy med [hög tillgänglighet till webb sidan för webb-och gateway-webbfrontend](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).
