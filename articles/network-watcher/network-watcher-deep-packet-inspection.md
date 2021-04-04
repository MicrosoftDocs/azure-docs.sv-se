---
title: Paket granskning med Azure Network Watcher | Microsoft Docs
description: Den här artikeln beskriver hur du använder Network Watcher för att utföra djup paket inspektion som samlas in från en virtuell dator
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3bd85d6faf05fcf659e9335ee9de3d64198dfa08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011177"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Paket granskning med Azure Network Watcher

Med funktionen för att hämta paket i Network Watcher kan du starta och hantera insamlings-sessioner på dina virtuella Azure-datorer från portalen, PowerShell, CLI och program mässigt via SDK och REST API. Med paket fångst kan du hantera scenarier som kräver paket nivå data genom att tillhandahålla informationen i ett användbart format. Genom att använda verktyg som är tillgängliga fritt för att granska data kan du undersöka kommunikation som skickas till och från dina virtuella datorer och få insikter om nätverks trafiken. Exempel på användning av paket insamlings data är: undersöker nätverks-eller program problem, identifierar nätverks missbruk och intrångs försök eller upprätthåller efterlevnad. I den här artikeln visar vi hur du öppnar en paket avbildnings fil som tillhandahålls av Network Watcher med ett populärt verktyg för öppen källkod. Vi kommer också att tillhandahålla exempel som visar hur du beräknar en anslutnings fördröjning, identifierar onormal trafik och undersöker nätverks statistik.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln går igenom några förkonfigurerade scenarier på en insamlad paket körning tidigare. De här scenarierna illustrerar funktioner som kan nås genom att granska en paket fångst. I det här scenariot används [wireshark](https://www.wireshark.org/) för att kontrol lera paket fångsten.

Det här scenariot förutsätter att du redan körde en paket avbildning på en virtuell dator. Information om hur du skapar ett paket fångst besök [hanterar paket fångster med portalen](network-watcher-packet-capture-manage-portal.md) eller med rest genom att besöka [Hantera paket insamlingar med REST API](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scenario

I det här scenariot:

* Granska en paket fångst

## <a name="calculate-network-latency"></a>Beräkna nätverks fördröjning

I det här scenariot visar vi hur du visar den inledande tur och retur tiden för en Transmission Control Protocol (TCP) som inträffar mellan två slut punkter.

När en TCP-anslutning upprättas följer de första tre paketen som skickas i anslutningen ett mönster som vanligt vis kallas för den tre-vägshandskakning. Genom att undersöka de två första paketen som skickas i den här hand skakningen, en första begäran från klienten och ett svar från servern, kan vi beräkna fördröjningen när den här anslutningen upprättades. Den här fördröjningen kallas för tur och retur-tid. Mer information om TCP-protokollet och den tre-vägs hand skakningen hittar du i följande resurs. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>Steg 1

Starta WireShark

### <a name="step-2"></a>Steg 2

Läs in **. Cap** -filen från paket fångsten. Den här filen finns i blobben som den sparades lokalt på den virtuella datorn, beroende på hur du konfigurerade den.

### <a name="step-3"></a>Steg 3

För att visa den första tur och retur-tiden i TCP-konversationer kommer vi bara att titta på de första två paketen som ingår i TCP-handskakningen. Vi kommer att använda de första två paketen i den tre-vägshandskakning, som är [SYN], [SYN, ACK]-paket. De namnges för flaggor som anges i TCP-huvudet. Det sista paketet i hand skakningen [ACK]-paketet kommer inte att användas i det här scenariot. [SYN]-paketet skickas av klienten. När servern har tagits emot skickar det [ACK]-paketet som en bekräftelse på att ta emot SYNen från klienten. Genom att dra nytta av att serverns svar kräver mycket lite omkostnader beräknar vi den för klar ande genom att subtrahera den tid då [SYN, ACK]-paketet togs emot av klienten med hjälp av Time [SYN]-paketet som skickades av klienten.

Med WireShark beräknas det här värdet för oss.

För att lättare kunna visa de två första paketen i den här högpresterande hand skakningen använder vi den filtrerings kapacitet som tillhandahålls av WireShark.

Om du vill använda filtret i WireShark expanderar du "Transmission Control Protocol"-segmentet för ett [SYN]-paket i insamlingen och undersöker de flaggor som angetts i TCP-huvudet.

Eftersom vi vill filtrera på alla [SYN]-och [SYN, ACK]-paket, under flaggor bekräftar du att tillståndet för syn-biten är inställt på 1. Högerklicka sedan på tillståndet syn-> Apply as filter-> markerat.

![Figur 7][7]

### <a name="step-4"></a>Steg 4

Nu när du har filtrerat fönstret så att det bara ser paket med bitarna [SYN], kan du enkelt välja konversationer som du är intresse rad av för att visa den inledande sökrutan. Ett enkelt sätt att Visa sökrutan i WireShark klickar du bara på list rutan som är markerad som "SEQ/ACK"-analys. Sedan visas den önskade visningen. I det här fallet var sökrutan var 0,0022114 sekunder eller 2,211 MS.

![Figur 8][8]

## <a name="unwanted-protocols"></a>Oönskade protokoll

Du kan ha många program som körs på en virtuell dator instans som du har distribuerat i Azure. Många av dessa program kommunicerar via nätverket, kanske utan uttrycklig behörighet. Genom att använda paket fångst för att lagra nätverkskommunikation kan vi undersöka hur programmet pratar i nätverket och leta efter eventuella problem.

I det här exemplet granskar vi en tidigare paket fångst för oönskade protokoll som kan indikera obehörig kommunikation från ett program som körs på datorn.

### <a name="step-1"></a>Steg 1

Använd samma avbildning i föregående scenario och klicka på **statistik**  >  **protokoll-hierarki**

![meny för protokoll-hierarki][2]

Fönstret protokollsekvens visas. I den här vyn visas en lista över alla protokoll som användes under insamlingsbufferten och antalet paket som överförts och tagits emot med protokollen. Den här vyn kan vara användbar för att hitta oönskad nätverks trafik på virtuella datorer eller i nätverket.

![hierarkin har öppnats][3]

Som du kan se i följande skärmdump användes trafiken med BitTorrent-protokollet, som används för peer-to-peer-fildelning. Som administratör kommer du inte att se BitTorrent-trafik på dessa specifika virtuella datorer. Nu när du känner till den här trafiken kan du ta bort peer-to-peer-programvaran som är installerad på den här virtuella datorn eller blockera trafiken med hjälp av nätverks säkerhets grupper eller en brand vägg. Dessutom kan du välja att köra paket insamlingar enligt ett schema, så att du kan granska protokollet på dina virtuella datorer regelbundet. Ett exempel på hur du automatiserar nätverks aktiviteter i Azure finns i [övervaka nätverks resurser med Azure Automation](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Hitta de vanligaste målen och portarna

Att förstå vilka typer av trafik, slut punkterna och portarna som kommunicerats över är viktigt vid övervakning eller fel sökning av program och resurser i nätverket. Genom att använda en paket insamlings fil från ovan kan vi snabbt lära dig de vanligaste målen som den virtuella datorn kommunicerar med och vilka portar som används.

### <a name="step-1"></a>Steg 1

Använd samma avbildning i föregående scenario och klicka på **statistik**  >  **IPv4 statistik**  >  **destinationer och portar**

![fönstret paket fångst][4]

### <a name="step-2"></a>Steg 2

När vi tittar igenom resultaten visas en rad med flera anslutningar på port 111. Den mest använda porten var 3389, som är fjärr skrivbord och de återstående dynamiska RPC-portarna.

Även om den här trafiken kan betyda ingenting, är det en port som används för många anslutningar och är okänd för administratören.

![Figur 5][5]

### <a name="step-3"></a>Steg 3

Nu när vi har fastställt en out-of-Place-port kan vi filtrera vår avbildning baserat på porten.

Filtret i det här scenariot är:

```
tcp.port == 111
```

Vi anger filter texten från ovan i text rutan Filter och trycker på RETUR.

![bild 6][6]

I resultatet ser vi all trafik från en lokal virtuell dator i samma undernät. Om vi fortfarande inte förstår varför den här trafiken sker kan vi ytterligare granska paketen för att avgöra varför den gör dessa anrop på port 111. Med den här informationen kan vi vidta lämpliga åtgärder.

## <a name="next-steps"></a>Nästa steg

Läs mer om de andra diagnostiska funktionerna i Network Watcher genom att gå till [Översikt över Azure Network Monitoring](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













