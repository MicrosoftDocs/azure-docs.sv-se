---
title: Diagnostisera grafik prestanda problem fjärr skrivbord – Azure
description: Den här artikeln beskriver hur du använder RemoteFX-grafikkort i sessioner med fjärr skrivbords protokoll för att diagnostisera prestanda problem med grafik i Windows Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: dc5c6499fa47a1e32a517032d5cc2a97b3f2677f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88007174"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnostisera problem med grafik prestanda i fjärr skrivbord

Om du vill diagnostisera upplevelsens kvalitets problem med dina fjärrsessioner har du fått räknare under avsnittet RemoteFX-grafik i prestanda övervakaren. Den här artikeln hjälper dig att hitta och åtgärda bildrelaterade prestanda Flask halsar under Remote Desktop Protocol (RDP)-sessioner med hjälp av dessa räknare.

## <a name="find-your-remote-session-name"></a>Hitta namnet på din fjärrsession

Du behöver Fjärrsessionens namn för att identifiera grafik prestanda räknarna. Följ anvisningarna i det här avsnittet för att identifiera din instans av varje räknare.

1. Öppna kommando tolken i Windows från fjärrsessionen.
2. Kör kommandot **Qwinsta** och leta reda på namnet på sessionen.
    - Om sessionen finns i en virtuell dator med flera sessioner (VM): din instans av varje räknare har suffix till samma nummer som suffixen för ditt sessionsnamn, till exempel RDP-TCP 37.
    - Om sessionen finns i en virtuell dator som har stöd för virtuella grafik processorer (vGPU): din instans av varje räknare lagras på servern i stället för på den virtuella datorn. Dina räknar instanser inkluderar det virtuella dator namnet i stället för numret i sessionsnamnet, till exempel "Win8 Enterprise VM".

>[!NOTE]
> Medan räknare har RemoteFX i sina namn, inkluderar de även fjärr skrivbords grafik i vGPU-scenarier.

## <a name="access-performance-counters"></a>Åtkomst till prestanda räknare

När du har bestämt namnet på fjärrsessionen följer du dessa anvisningar för att samla in prestanda räknare för RemoteFX-bilder för din fjärrsession.

1. Välj **Starta**  >  **administrations verktyg**  >  **prestanda övervakaren**.
2. I dialog rutan **prestanda övervakaren** expanderar du **övervaknings verktyg**, väljer **prestanda övervakare** och väljer sedan **Lägg till**.
3. I dialog rutan **Lägg till räknare** , i listan **tillgängliga räknare** , expanderar du avsnittet för RemoteFX-grafik.
4. Välj de räknare som ska övervakas.
5. I listan **instanser av valt objekt** väljer du de instanser som ska övervakas för de valda räknarna och väljer sedan **Lägg till**. Om du vill välja alla tillgängliga räknar instanser väljer du **alla instanser**.
6. När du har lagt till räknarna väljer du **OK**.

De valda prestanda räknarna visas på skärmen prestanda övervakaren.

>[!NOTE]
>Varje aktiv session på en värd har en egen instans av varje prestanda räknare.

## <a name="diagnose-issues"></a>Diagnostisera problem

Grafik-relaterade prestanda problem finns vanligt vis i fyra kategorier:

- Låg bild frekvens
- Slumpmässiga bås
- Svars tid med hög svars tid
- Dålig ram-kvalitet

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Ta itu med låg bild frekvens, slumpmässiga bås och lång svars tid för svars tider

Kontrol lera först räknarna för utdata/sekund. Det mäter antalet ramar som gjorts tillgängliga för klienten. Om det här värdet är mindre än räknarna för indatamängder/sekund hoppas ramarna över. Om du vill identifiera Flask halsen använder du bild rutor som hoppats över/sekund räknare.

Det finns tre typer av ramar som hoppas över/andra räknare:

- Överhoppade bild rutor per sekund (otillräckliga server resurser)
- Överhoppade bild rutor per sekund (otillräckliga nätverks resurser)
- Överhoppade bild rutor per sekund (otillräckliga klient resurser)

Ett högt värde för någon av de överhoppade bild rutorna/sekund räknarna innebär att problemet är relaterat till den resurs som räknaren spårar. Om klienten till exempel inte avkodar och presenterar bild rutor i samma takt som servern innehåller ramarna, kommer bild rutorna som hoppades över/sekund (otillräckliga klient resurser) att vara höga.

Om räknarna för utdata/sekund matchar indata-och sekund räknare, trots att du fortfarande märker ovanliga fördröjningar eller fickor, kan den genomsnittliga kodnings tiden vara orsaken. Encoding är en synkron process som inträffar på servern i vGPU-scenariot (Single-session) och på den virtuella datorn i scenariot för flera sessioner. Genomsnittlig kodnings tid ska vara under 33 MS. Om den genomsnittliga kodnings tiden är under 33 MS men du fortfarande har prestanda problem, kan det bero på ett problem med den app eller det operativ system som du använder.

För ytterligare information om hur du diagnostiserar problem med appar, se [prestanda räknare](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)för användarindata.

Eftersom RDP stöder en genomsnittlig kodnings tid på 33 MS, stöder den en bild Rute frekvens på upp till 30 bild rutor per sekund. Observera att 33 MS är den maximala bild Rute frekvensen som stöds. I många fall kommer bild frekvensen som användaren upplever att vara lägre, beroende på hur ofta en ram tillhandahålls till RDP av källan. Till exempel kan aktiviteter som att titta på en video kräva en full bilds frekvens på 30 bild rutor per sekund, men mindre beräknings intensiva uppgifter som att sällan redigera ett dokument resulterar i ett mycket lägre värde för indata-ramar/sekund utan försämring i användarens upplevelse kvalitet.

### <a name="addressing-poor-frame-quality"></a>Hantera dålig ram-kvalitet

Använd ram kvalitets räknaren för att diagnosticera problem med frame-kvalitet. Den här räknaren uttrycker kvaliteten på utmatnings ramen som en procent andel av käll ramens kvalitet. Kvalitets förlusten kan bero på att RemoteFX eller grafik källan kan vara inbyggd. Om RemoteFX orsakade kvalitets förlusten kan problemet bero på brist på nätverks-eller server resurser för att skicka innehåll med högre åter givning.

## <a name="mitigation"></a>Åtgärd

Om server resurser orsakar Flask halsen kan du prova någon av följande metoder för att förbättra prestanda:

- Minska antalet sessioner per värd.
- Öka minnes-och beräknings resurserna på servern.
- Ta bort anslutningens upplösning.

Om nätverks resurserna orsakar Flask halsen kan du prova någon av följande metoder för att förbättra nätverks tillgänglighet per session:

- Minska antalet sessioner per värd.
- Använd ett nätverk med större bandbredd.
- Ta bort anslutningens upplösning.

Om klient resurserna orsakar Flask halsen kan du prova någon av följande metoder för att förbättra prestandan:

- Installera den senaste fjärr skrivbords klienten.
- Öka minnes-och beräknings resurserna på klient datorn.

> [!NOTE]
> Vi stöder för närvarande inte käll ramarna/sekund räknaren. För tillfället visar räknaren för käll ramar och sekund alltid 0.

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en GPU-optimerad virtuell Azure-dator finns i [Konfigurera GPU-acceleration (Graphics Processing Unit) för Windows Virtual Desktop-miljö](configure-vm-gpu.md).
- En översikt över fel sökning och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview.md).
- Mer information om tjänsten finns i [Skriv bords miljö i Windows](environment-setup.md).
