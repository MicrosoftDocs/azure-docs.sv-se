---
title: Remote Desktop Protocol bandbredds krav Windows Virtual Desktop – Azure
titleSuffix: Azure
description: Förstå kraven för RDP-bandbredd för Windows Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: c9ef72241ac62efd8555de59bb52949321364035
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96325187"
---
# <a name="remote-desktop-protocol-rdp-bandwidth-requirements"></a>Krav för bandbredd för Remote Desktop Protocol (RDP)

Remote Desktop Protocol (RDP) är en sofistikerad teknik som använder olika metoder för att perfekt leverera serverns fjärrgrafik till klient enheten. Beroende på användnings fall, tillgänglighet för dator resurser och nätverks bandbredd justerar RDP dynamiskt olika parametrar för att leverera den bästa användar upplevelsen.

Remote Desktop Protocol flera Plex flera dynamiska virtuella kanaler (DVCs) till en enda data kanal som skickas över olika nätverks transporter. Det finns separata DVCs för fjärrgrafik, ininformation, omdirigering av enheter, utskrift och andra. WVD-partner kan också implementera sina tillägg som använder enheter-gränssnitt.
Mängden data som skickas via RDP beror på användarens aktivitet. En användare kan till exempel arbeta med grundläggande text innehåll för merparten av sessionen och förbruka minimal bandbredd, men sedan generera en utskrift av ett dokument på 200-sidan till den lokala skrivaren. Det här utskrifts jobbet kommer att använda en stor mängd nätverks bandbredd.

När du använder en fjärrsession, påverkar nätverkets tillgängliga bandbredd dramatiskt kvaliteten på din upplevelse. Olika program och visnings lösningar kräver olika nätverkskonfigurationer, så det är viktigt att se till att din nätverks konfiguration uppfyller dina behov.

## <a name="estimating-bandwidth-utilization"></a>Beräkna bandbredds användning

RDP använder olika kompressions algoritmer för olika typer av data. Tabellen nedan visar en uppskattning av data överföringar:

| Typ av Data | Riktning | Beräkna |
|---|---|---|
| Fjärran sluten grafik | Sessions värd till klient | [Se de detaljerade rikt linjerna](#estimating-bandwidth-used-by-remote-graphics) |
| Pulsslag | Båda riktningarna | ~ 20 byte var femte sekund  |
| Indata | Värd för klient till session | Mängden data baseras på användar aktiviteten, mindre än 100 byte för de flesta åtgärder  |
| Fil överföringar | Båda riktningarna | Fil överföringar använder Mass komprimering. Använd. zip-komprimering för uppskattning |
| Skriva ut | Sessions värd till klient | Utskrift av utskrifts jobb är beroende av driv rutinen och använder Mass komprimering. zip-komprimering för tillnärmning |

I andra scenarier kan bandbredds kraven ändras beroende på hur du använder dem, till exempel:

* Röst-eller video konferens
* Kommunikation i real tid
* Strömmande 4K-video

## <a name="estimating-bandwidth-used-by-remote-graphics"></a>Beräkna bandbredden som används av fjärrgrafik

Det är svårt att förutse bandbredds användningen av fjärr skrivbordet. Användar aktiviteterna genererar merparten av fjärr skrivbords trafiken. Varje användare är unik och skillnader i deras arbets mönster kan ändra nätverks användningen markant.

Det bästa sättet att förstå bandbredds kraven är att övervaka riktiga användar anslutningar. Övervakning kan utföras av inbyggda prestanda räknare eller av nätverks utrustningen.

I många fall kan du dock beräkna nätverks användningen genom att förstå hur Remote Desktop Protocol fungerar och genom att analysera användarnas arbets mönster.

Remote Protocol levererar grafiken som genereras av fjärrservern för att visa den på en lokal övervakare. Mer specifikt ger den Skriv bords bitmappen helt sammansatt på servern.
När du skickar en Skriv bords bitmapp verkar som en enkel aktivitet i första hand, kräver det en stor mängd resurser. Till exempel är en 1080p Desktop-avbildning i dess okomprimerade form ungefär 8 MB i storlek. Den här bilden visas på den lokalt anslutna övervakaren med en liten skärm uppdaterings frekvens på 30 Hz som kräver bandbredd på cirka 237 MB/s.

För att minska mängden data som överförs via nätverket, använder RDP kombinationen av flera tekniker, inklusive men inte begränsat till

* Optimeringar för bild Rute frekvens
* Klassificering av skärm innehåll
* Innehålls-/regionsspecifika codec
* Progressiv bild kodning
* Cachelagring på klient Sidan

Tänk på följande för att bättre förstå fjärrgrafik:

* Den rikare grafiken, mer bandbredd som ska vidtas
  * Text, GRÄNSSNITTs element för fönster och Solid Color-områden förbrukar mindre bandbredd än vad som annars är.
  * Naturliga avbildningar är de viktigaste bidrags givarna för bandbredds användning. Men cachelagring på klient sidan bidrar till att minska.
* Endast ändrade delar av skärmen överförs. Om det inte finns några synliga uppdateringar på skärmen skickas inga uppdateringar.
* Videouppspelning och annan hög bilds ätt innehåll är i stort sett ett bild spel. RDP använder dynamiskt lämpliga video-codecs för att leverera dem till den ursprungliga bild frekvensen. Men det är fortfarande grafik och det är fortfarande den viktigaste deltagaren i bandbredds användningen.
* Tids inaktivitet i fjärr skrivbord innebär inga eller minimala skärm uppdateringar. nätverks användningen är därför minimal vid inaktivitet.
* När fönstret fjärr skrivbords klient minimeras skickas inga grafiska uppdateringar från sessionens värd.

Tänk på att belastningen på ditt nätverk är beroende av både bild frekvensen för din app-arbetsbelastning och din skärmupplösning. Om bild frekvensen eller skärmupplösningen ökar, kommer bandbredds kravet också att öka. En ljus arbets belastning med hög upplöst visning kräver till exempel mer tillgänglig bandbredd än en lätt arbets belastning med regelbunden eller låg upplösning. Olika visnings lösningar kräver olika tillgängliga bandbredder.

Tabellen nedan visar en uppskattning av de data som används i olika grafik scenarier. Dessa siffror gäller för en enda övervaknings konfiguration med 1920x1080-upplösning och med både standard grafik läge och H. 264/AVC 444-grafik läge.

| Scenario | Standard läge | H. 264/AVC 444-läge | Miniatyr | Beskrivning av scenariot |
|:---|---:|---:|---|---|
| Inaktiv | 0,3 kbps | 0,3 kbps |:::image type="content" source="media/idle.png" alt-text="Skärm bild av den inaktiva anslutningen":::| Användaren har pausat sitt arbete och det finns inga aktiva skärm uppdateringar |
| Microsoft Word | 100-150 kbps | 200-300 kbps |:::image type="content" source="media/word.gif" alt-text="Animering av Microsoft Word":::| Användaren arbetar aktivt med Microsoft Word, skriver, klistrar in grafik och växlar mellan dokument |
| Microsoft Excel | 150-200 kbps | 400-500 kbps |:::image type="content" source="media/excel.gif" alt-text="Animering av Microsoft Excel":::| Användaren arbetar aktivt med Microsoft Excel, flera celler med formler och diagram uppdateras samtidigt |
| Microsoft PowerPoint | 4 – 4,5 Mbit/s | 1,6-1,8 Mbit/s |:::image type="content" source="media/powerpoint.gif" alt-text="Animering av Microsoft PowerPoint":::| Användaren arbetar aktivt med Microsoft PowerPoint, skriver, klistrar in. Användaren kan också ändra omfattande grafik och använda bild över gångs effekter |
| Webbs ökning | 6 – 6,5 Mbit/s | 0,9 – 1 Mbit/s |:::image type="content" source="media/web.gif" alt-text="Animering av webb läsning":::| Användaren arbetar aktivt med en grafiskt omfattande webbplats som innehåller flera statiska och animerade bilder. Användaren rullar sidorna både vågrätt och lodrätt |
| Bild galleri | 3,3 – 3.6 Mbit/s | 0,7 – 0,8 Mbit/s |:::image type="content" source="media/image-gallery.gif" alt-text="Animering av bild galleriet":::| Användaren arbetar aktivt med programmet bild galleri. Bläddra, zooma, ändra storlek på och rotera bilder |
| Videouppspelning | 8,5 – 9,5 Mbit/s | 2,5-2,8 Mbit/s |:::image type="content" source="media/video.gif" alt-text="Animering av videouppspelning":::| Användaren tittar på en 30 FPS-video som förbrukar 1/2 av skärmen |
| Video uppspelning på skärm bilden | 7,5 – 8.5 Mbit/s | 2,5-3,1 Mbit/s |:::image type="content" source="media/fullscreen-video.gif" alt-text="Animering av uppspelning av hel skärms video":::| Användaren tittar på en 30 FPS-video som maximeras till en hel skärms läge |

## <a name="dynamic-bandwidth-allocation"></a>Allokering av dynamisk bandbredd

Remote Desktop Protocol är ett modernt protokoll som utformats för att anpassa sig till de ändrade nätverks förhållandena dynamiskt.
I stället för att använda hård gränserna för bandbredds användning använder RDP kontinuerlig nätverks identifiering som aktivt övervakar tillgänglig nätverks bandbredd och fördröjning för paket fördröjning. Enligt undersöknings resultaten väljer RDP dynamiskt grafik kodnings alternativ och allokerar bandbredd för omdirigering av enheter och andra virtuella kanaler.  
Den här tekniken gör det möjligt för RDP att använda den fullständiga nätverks röret när det är tillgängligt och snabbt kan stänga av när nätverket behövs för något annat.
RDP identifierar och justerar bild kvalitet, bild frekvens eller komprimerings algoritmer om andra program begär nätverket.

## <a name="limit-network-bandwidth-use-with-throttle-rate"></a>Begränsa användningen av nätverks bandbredd med begränsnings frekvens

I de flesta fall behöver du inte begränsa bandbredds användningen eftersom begränsningen kan påverka användar upplevelsen. Du kanske vill begränsa nätverks användningen än i de begränsade nätverken. Ett annat exempel är lånade nätverk som debiteras för den mängd trafik som används.

I sådana fall kan du begränsa en utgående RDP-nätverks trafik genom att ange en begränsnings nivå i QoS-principen.

  >[!NOTE]
  > [Kontrol lera att RDP-Shortpath har Aktiver ATS](./shortpath.md) – begränsning av begränsad hastighet stöds inte för omvänd Connect-transport.

### <a name="implement-throttle-rate-limiting-on-session-host-using-group-policy"></a>Implementera begränsnings frekvensen på en värd med hjälp av grupprincip

Du kan använda principbaserad tjänst kvalitet (QoS) i grupprincip för att ange den fördefinierade begränsnings hastigheten.

Om du vill skapa en QoS-princip för domänanslutna sessionsbaserade värdar loggar du först in på en dator där grupprincip hantering har installerats. Öppna grupprincip hantering (Välj Start, peka på administrations verktyg och välj sedan grupprincip hantering) och utför sedan följande steg:

1. Leta upp den behållare där den nya principen ska skapas i grupprincip hantering. Om till exempel alla din session är värd för datorer i en ORGANISATIONSENHET med namnet **session hosts**, ska den nya principen skapas i sessionen är värd för organisationsenheten.

2. Högerklicka på lämplig behållare och välj sedan **skapa ett grup princip objekt i den här domänen och länka det här**.

3. I dialog rutan **nytt grup princip objekt** anger du ett namn för det nya Grupprincip-objektet i rutan **namn** och väljer sedan **OK**.

4. Högerklicka på den nyligen skapade principen och välj sedan **Redigera**.

5. I Redigeraren Grupprinciphantering expanderar du **dator konfiguration**, expanderar **Windows-inställningar**, högerklickar på **Principbaserad QoS** och väljer sedan **Skapa ny princip**.

6. I dialog rutan **Principbaserad QoS** anger du ett namn för den nya principen i rutan **namn** på sidan öppna. Välj **ange utgående begränsnings pris** och ange det nödvändiga värdet och välj sedan **Nästa**.

7. På nästa sida väljer du **endast program med det här namnet på den körbara filen** och anger namnet **svchost.exe** och väljer sedan **Nästa**. Den här inställningen instruerar principen att endast prioritera matchande trafik från fjärr skrivbords tjänsten.

8. På den tredje sidan kontrollerar du att både **käll-IP-adressen** och **alla mål-IP-adresser** är markerade. Välj **Nästa**. De här två inställningarna ser till att paketen hanteras oavsett vilken dator (IP-adress) som skickade paketen och vilken dator (IP-adress) som ska ta emot paketen.

9. På sidan fyra väljer du **UDP** från listan **Välj det protokoll som den här QoS-principen gäller för i** List rutan.

10. Under rubriken **anger du käll port nummer väljer du** **från den här käll porten eller det här intervallet**. Skriv **3390** i den tillhör ande text rutan. Välj **Slutför**.

De nya principerna som du har skapat börjar inte gälla förrän grupprincip har uppdaterats på datorns värddatorer för sessionen. Även om grupprincip uppdateras regelbundet, kan du framtvinga en omedelbar uppdatering genom att följa dessa steg:

1. Öppna en kommando tolk som administratör (*Kör som administratör*) på varje värd för sessionen som du vill uppdatera Grupprincip.

2. I kommando tolken anger du

   ```console
   gpupdate /force
   ```

### <a name="implement-throttle-rate-limiting-on-session-host-using-powershell"></a>Implementera begränsnings frekvensen på en värd med hjälp av PowerShell

Du kan ange begränsnings nivå för RDP-Shortpath med PowerShell-cmdleten nedan:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om bandbredds krav för Windows Virtual Desktop, se [förstå Remote Desktop Protocol (RDP) bandbredds krav för virtuella Windows-datorer](rdp-bandwidth.md).
* Om du vill veta mer om anslutning till virtuella Windows-datorer i Windows kan du läsa om [nätverks anslutning i Windows Virtual Desktop](network-connectivity.md).
* För att komma igång med tjänst kvalitet (QoS) för Windows Virtual Desktop, se [implementera tjänst kvalitet (QoS) för Windows Virtual Desktop](rdp-quality-of-service-qos.md).
