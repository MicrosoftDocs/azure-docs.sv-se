---
title: Implementera tjänst kvalitet (QoS) för Windows Virtual Desktop (för hands version)
titleSuffix: Azure
description: Konfigurera QoS (för hands version) för Windows Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94639427"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>Implementera tjänst kvalitet (QoS) för Windows Virtual Desktop (för hands version)

> [!IMPORTANT]
> QoS-principer (Quality of Service) för virtuella Windows-datorer är för närvarande en offentlig för hands version.
> Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[RDP-Shortpath](./shortpath.md) ger en direkt UDP-baserad transport mellan fjärr skrivbords klienten och sessionsvärdservern. RDP-Shortpath möjliggör konfiguration av QoS-principer (Quality of Service) för RDP-data.
QoS i Windows Virtual Desktop tillåter real tids RDP-trafik som är känslig för nätverks fördröjningar till "klipp i linje" framför trafik som är mindre känslig. Exempel på sådan mindre känslig trafik är att ladda ned en ny app, där en extra sekund att ladda ned inte är ett stort avtal. QoS använder Windows grupprincip objekt för att identifiera och markera alla paket i real tids strömmar och hjälpa nätverket att ge RDP-trafik en dedikerad del av bandbredden.

Om du har stöd för en stor grupp användare som har problem som beskrivs i den här artikeln måste du förmodligen implementera QoS. Ett litet företag med få användare kanske inte behöver QoS, men det bör vara användbart även där.

Utan någon form av QoS kan du se följande problem:

* Darr – RDP-paket som kommer till olika hastigheter, vilket kan leda till visuella objekt och ljud fel
* Paket förlust – paket som släppts, vilket resulterar i återöverföring som kräver ytterligare tid
* Fördröjd tur (Round-Trip Time) – RDP-paket tar lång tid att komma åt sina destinationer, vilket resulterar i en märkbar fördröjning mellan ingångs-och reaktions tider från fjärrprogrammet.

Det minsta komplicerade sättet att åtgärda dessa problem är att öka storleken på data anslutningarna, både internt och från Internet. Eftersom det ofta är kostnads fritt att använda QoS, är QoS ett sätt att hantera de resurser du har i stället för att öka bandbredden mer effektivt. För att lösa kvalitets problem rekommenderar vi att du först använder QoS och sedan lägger till bandbredd endast vid behov.

För att QoS ska vara effektivt måste du tillämpa konsekventa QoS-inställningar i hela organisationen. Vilken del som helst av sökvägen som inte stöder QoS-prioriteringar kan försämra RDP-sessionens kvalitet.

## <a name="introduction-to-qos-queues"></a>Introduktion till QoS-köer

För att tillhandahålla QoS måste nätverks enheter ha ett sätt att klassificera trafik och måste kunna skilja på RDP från annan nätverks trafik.

När nätverks trafik går in i en router placeras trafiken i en kö. Om en QoS-princip inte är konfigurerad finns det bara en kö och alla data behandlas som först in, först ut med samma prioritet. Det innebär att RDP-trafik kan bli fastnad bakom trafik där några extra fördröjningar i millisekunder inte skulle vara ett problem.

När du implementerar QoS definierar du flera köer med hjälp av en av flera överbelastnings hanterings funktioner, till exempel Ciscos prioriterade köer och [klassbaserade, viktade, CBWFQ](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) och överbelastnings störningar, till exempel [viktad slumpmässig tidig identifiering (WRED)](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html).

En enkel analog är att QoS skapar virtuella "Carpool-band" i ditt data nätverk. Det innebär att vissa typer av data aldrig eller sällan påträffar en fördröjning. När du har skapat de här banden kan du justera deras relativa storlek och mycket mer effektiv hantering av anslutnings bandbredden medan du fortfarande levererar erfarenheter i affärs klass för din organisations användare.

## <a name="qos-implementation-checklist"></a>Check lista för QoS-implementering

På hög nivå gör du följande för att implementera QoS:

1. [Kontrol lera att nätverket är klart](#make-sure-your-network-is-ready)
2. [Kontrol lera att RDP-Shortpath är aktive rad](./shortpath.md) -QoS-principer stöds inte för omvänd Connect-transport
3. [Implementera infogning av DSCP-markörer](#insert-dscp-markers) på sessionsbaserade värdar

När du förbereder för att implementera QoS bör du tänka på följande rikt linjer:

* Den kortaste sökvägen till Session Host är bäst
* Eventuella hinder i mellan, t. ex. proxyservrar eller paket kontroll enheter, rekommenderas inte

## <a name="make-sure-your-network-is-ready"></a>Kontrol lera att nätverket är klart

Om du överväger att använda en QoS-implementering bör du redan ha fastställt kraven på bandbredd och andra [nätverks krav](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context).
  
Trafik överbelastning i ett nätverk påverkar markant medie kvaliteten. Brist på bandbredd leder till försämrade prestanda och en dålig användar upplevelse. Använd [Log Analytics](./diagnostics-log-analytics.md) för att identifiera problem och göra justeringar med hjälp av QoS och tillägg för selektiv bandbredd när Windows Virtual Desktop-antagande och användning växer.

### <a name="vpn-considerations"></a>VPN-överväganden

QoS fungerar bara som förväntat när det implementeras på alla länkar mellan klienter och värdbaserade sessioner. Om du använder QoS i ett internt nätverk och en användare loggar in från en annan plats kan du bara prioritera i ditt interna, hanterade nätverk. Även om fjärranslutna platser kan ta emot en hanterad anslutning genom att implementera ett virtuellt privat nätverk (VPN) lägger en VPN till paket belastning och skapar fördröjningar i real tids trafik.

I en global organisation med hanterade länkar som omfattar kontinenter rekommenderar vi att du använder QoS eftersom bandbredden för dessa länkar är begränsad jämfört med det lokala nätverket.

## <a name="insert-dscp-markers"></a>Infoga DSCP-markörer

Du kan implementera QoS med hjälp av ett grupprincip objekt (GPO) som dirigerar värdar för att infoga en DSCP-markör i IP-pakethuvuden som identifierar den som en viss typ av trafik. Routrar och andra nätverks enheter kan konfigureras för att identifiera dessa märkningar och placera trafiken i en separat kö med högre prioritet.

Du kan jämföra DSCP-markeringar med post märken som visar att det är så viktigt att leveransen är och hur brådskande leveransen är och hur det är bäst att sortera det. När du har konfigurerat nätverket så att det ger prioritet till RDP-strömmar, bör förlorade paket och sena paket minska avsevärt.

När alla nätverks enheter använder samma klassificeringar, markeringar och prioriteringar, är det möjligt att minska eller eliminera fördröjningar, tappade paket och darr. I RDP-perspektivet är det grundläggande konfigurations steget klassificering och markering av paket. För att fullständig QoS ska lyckas måste du också justera RDP-konfigurationen med den underliggande nätverks konfigurationen noggrant.
DSCP-värdet anger ett motsvarande konfigurerat nätverk vilken prioritet som ska ge ett paket eller en data ström.

Vi rekommenderar att du använder DSCP-värdet 46 som mappar till en **(EF) DSCP-klass med påskyndad vidarebefordring** .

### <a name="implement-qos-on-session-host-using-group-policy"></a>Implementera QoS på sessionsvärdservern med hjälp av grupprincip

Du kan använda principbaserad tjänst kvalitet (QoS) i grupprincip för att ange det fördefinierade DSCP-värdet.

Om du vill skapa en QoS-princip för domänanslutna sessionsbaserade värdar loggar du först in på en dator där grupprincip hantering har installerats. Öppna grupprincip hantering (Välj Start, peka på administrations verktyg och välj sedan grupprincip hantering) och utför sedan följande steg:

1. Leta upp den behållare där den nya principen ska skapas i grupprincip hantering. Om till exempel alla din session är värd för datorer i en ORGANISATIONSENHET med namnet **"sessioner"**, ska den nya principen skapas i sessionen är värd för organisationsenheten.

2. Högerklicka på lämplig behållare och välj sedan **skapa ett grup princip objekt i den här domänen och länka det här**.

3. I dialog rutan **nytt grup princip objekt** anger du ett namn för det nya Grupprincip-objektet i rutan **namn** och väljer sedan **OK**.

4. Högerklicka på den nyligen skapade principen och välj sedan **Redigera**.

5. I Redigeraren Grupprinciphantering expanderar du **dator konfiguration**, expanderar **Windows-inställningar**, högerklickar på **Principbaserad QoS** och väljer sedan **Skapa ny princip**.

6. I dialog rutan **Principbaserad QoS** anger du ett namn för den nya principen i rutan **namn** på sidan öppna. Välj **Ange DSCP-värde** och Ställ in värdet på **46**. Lämna **ange utgående begränsnings avgift** omarkerad och välj sedan **Nästa**.

7. På nästa sida väljer du **endast program med det här namnet på den körbara filen** och anger namnet **svchost.exe** och väljer sedan **Nästa**. Den här inställningen instruerar principen att endast prioritera matchande trafik från fjärr skrivbords tjänsten.

8. På den tredje sidan ser du till att både **käll-IP-adressen** och **alla mål-IP-adresser** är markerade och väljer sedan **Nästa**. De här två inställningarna ser till att paketen hanteras oavsett vilken dator (IP-adress) som skickade paketen och vilken dator (IP-adress) som ska ta emot paketen.

9. På sidan fyra väljer du **UDP** från listan **Välj det protokoll som den här QoS-principen gäller för i** List rutan.

10. Under rubriken **anger du käll port nummer väljer du** **från den här käll porten eller det här intervallet**. Skriv **3390** i den tillhör ande text rutan. Välj **Slutför**.

De nya principerna som du har skapat börjar inte gälla förrän grupprincip har uppdaterats på datorns värddatorer för sessionen. Även om grupprincip uppdateras regelbundet, kan du framtvinga en omedelbar uppdatering genom att följa dessa steg:

1. Öppna en kommando tolk som administratör (*Kör som administratör*) på varje värd för sessionen som du vill uppdatera Grupprincip.

1. I kommando tolken anger du

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>Implementera QoS på sessionsvärdservern med PowerShell

Du kan ställa in QoS för RDP-Shortpath med PowerShell-cmdleten nedan:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>Relaterade artiklar

* [QoS-princip (Quality of Service)](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om bandbredds krav för Windows Virtual Desktop, se [förstå Remote Desktop Protocol (RDP) bandbredds krav för virtuella Windows-datorer](rdp-bandwidth.md).
* Om du vill veta mer om anslutning till virtuella Windows-datorer i Windows kan du läsa om [nätverks anslutning i Windows Virtual Desktop](network-connectivity.md).
