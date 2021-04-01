---
title: Förstå nätverks anslutningar för virtuella Windows-datorer
titleSuffix: Azure
description: Lär dig mer om Windows Virtual Desktop nätverks anslutning
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: e4149864e16196b695d38a8c46ab5af835453412
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221218"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>Förstå nätverks anslutningar för virtuella Windows-datorer

Med Windows Virtual Desktop kan du vara värd för klientsessioner på de sessioner som körs på Azure. Microsoft hanterar delar av tjänsterna för kundens räkning och ger säkra slut punkter för att ansluta klienter och värdar för sessioner. Diagrammet nedan ger en översikt över de nätverks anslutningar som används av virtuella Windows-datorer

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Diagram över nätverks anslutningar för virtuella Windows-datorer" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>Sessionsanslutning

Windows Virtual Desktop använder Remote Desktop Protocol (RDP) för att tillhandahålla fjärrskärmar och ingångs funktioner över nätverks anslutningar. RDP har ursprungligen lanserats med Windows NT 4,0 Terminal Server Edition och utvecklas kontinuerligt med alla Microsoft Windows-och Windows Server-versioner. Från början har RDP utvecklat för att vara oberoende av dess underliggande transport stack och idag stöder de flera typer av transporter.

## <a name="reverse-connect-transport"></a>Omvänd Connect-transport

Windows Virtual Desktop använder omvänd Connect-transport för att upprätta fjärrsessionen och för att transportera RDP-trafik. Till skillnad från de lokala Fjärrskrivbordstjänster distributioner använder omvänd Connect transport inte en TCP-lyssnare för att ta emot inkommande RDP-anslutningar. I stället använder den utgående anslutningen till Windows-infrastrukturen för virtuella skriv bord via HTTPS-anslutningen.

## <a name="session-host-communication-channel"></a>Kommunikations kanal för Session Host

Vid start av Windows-sessionsvärdservern för virtuella skriv bord upprättar tjänsten Remote Desktop agent Loader den beständiga kommunikations kanalen i Windows Virtual Desktop Broker. Den här kommunikations kanalen ligger ovanpå en säker Transport Layer Security-anslutning (TLS) och fungerar som en buss för service Message Exchange mellan Session Host och Windows Virtual Desktop Infrastructure.

## <a name="client-connection-sequence"></a>Klient anslutnings ordning

Den sekvens för klient anslutning som beskrivs nedan:

1. Använda Windows-klienten för virtuella skriv bord som stöds i Windows Virtual Desktop-arbetsytan
2. Azure Active Directory autentiserar användaren och returnerar den token som används för att räkna upp resurser som är tillgängliga för en användare
3. Klienten skickar token till prenumerations tjänsten för Windows Virtual Desktop-feed
4. Prenumerations tjänsten för Windows Virtual Desktop-feed verifierar token
5. Prenumerations tjänsten för Windows virtuell Skriv bords matning skickar listan över tillgängliga Skriv bord och RemoteApp-fönster till klienten i form av digitalt signerad anslutnings konfiguration
6. Klienten lagrar anslutnings konfigurationen för varje tillgänglig resurs i en uppsättning RDP-filer
7. När en användare väljer den resurs som ska ansluta använder klienten den associerade. RDP-filen och upprättar en säker TLS 1,2-anslutning till den närmaste Windows-gatewayen för virtuella skriv bord och skickar anslutnings informationen
8. Windows Virtual Desktop-gatewayen verifierar begäran och ber Windows Virtual Desktop Broker att dirigera anslutningen
9. Windows Virtual Desktop Broker identifierar värd för sessionen och använder den tidigare etablerade permanenta kommunikations kanalen för att initiera anslutningen
10. Med fjärr skrivbords stacken initieras TLS 1,2-anslutningen till samma instans av Windows Virtual Desktop Gateway som används av klienten
11. När både klienten och sessionen är ansluten till gatewayen börjar gatewayen att vidarebefordra rå data mellan båda slut punkterna. det här upprättar basen omvänd anslutning för RDP
12. När bas transporten har angetts startar klienten RDP-handskakningen

## <a name="connection-security"></a>Anslutningssäkerhet

TLS 1,2 används för alla anslutningar som initieras från klienterna och värdarna i Windows infrastruktur komponenter för virtuella datorer. Windows Virtual Desktop använder samma TLS 1,2-chiffer som i [Azures front dörr](../frontdoor/front-door-faq.md#what-are-the-current-cipher-suites-supported-by-azure-front-door). Det är viktigt att se till att både klient datorer och sessioner kan använda dessa chiffer.
För omvänd Connect-transport ansluter både klienten och sessionens värd till Windows Virtual Desktop-gatewayen. När TCP-anslutningen har upprättats verifierar klienten eller sessionen Windows Virtual Desktop Gateway-certifikatet.
När du har upprättat bas transporten upprättar RDP en kapslad TLS-anslutning mellan klient-och sessionstoken med hjälp av sessionens värddators certifikat. Som standard genereras certifikatet som används för RDP-kryptering självt av operativ systemet under distributionen. Om du vill kan kunderna distribuera centralt hanterade certifikat som utfärdats av utfärdaren av företags certifikat utfärdaren. Mer information om hur du konfigurerar certifikat finns i [dokumentationen till Windows Server](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om bandbredds krav för Windows Virtual Desktop, se [förstå Remote Desktop Protocol (RDP) bandbredds krav för virtuella Windows-datorer](rdp-bandwidth.md).
* För att komma igång med tjänst kvalitet (QoS) för Windows Virtual Desktop, se [implementera tjänst kvalitet (QoS) för Windows Virtual Desktop](rdp-quality-of-service-qos.md).
