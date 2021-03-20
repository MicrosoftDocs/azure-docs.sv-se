---
title: Övervakning och fel sökning från HANA-sida på SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Övervakning och fel sökning från HANA-sida på SAP HANA på en Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83743a6985bef8ce6c03e01ed8d10aa740852106
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668804"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Övervaka och felsöka från HANA-sida

För att effektivt kunna analysera problem som rör SAP HANA på Azure (stora instanser) är det bra att begränsa rotor saken till ett problem. SAP har publicerat en stor mängd dokumentation för att hjälpa dig.

Tillämpliga vanliga frågor och svar om SAP HANA prestanda finns i följande SAP-anteckningar:

- [SAP-anteckning #2222200 – vanliga frågor och svar: SAP HANA nätverk](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP-anteckning #2100040 – vanliga frågor och svar: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP-anteckning #199997 – vanliga frågor och svar: SAP HANA minne](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP-anteckning #200000 – vanliga frågor och svar: SAP HANA prestanda optimering](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP-anteckning #199930 – vanliga frågor och svar: SAP HANA I/O-analys](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP-anteckning #2177064 – vanliga frågor och svar: SAP HANA omstart och krascher](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA aviseringar

Som ett första steg kontrollerar du de aktuella SAP HANA aviserings loggarna. I SAP HANA Studio går du till **administrations konsolen: aviseringar: Visa: alla aviseringar**. På den här fliken visas alla SAP HANA aviseringar för vissa värden (ledigt fysiskt minne, processor användning osv.) som faller utanför de angivna minimi-och Max tröskelvärdena. Som standard uppdateras kontrollerna automatiskt var 15: e minut.

![I SAP HANA Studio går du till administrations konsolen: aviseringar: Visa: alla aviseringar](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>Processor

En avisering som utlöses på grund av en felaktig tröskel inställning är att återställa till standardvärdet eller ett mer rimligt tröskelvärde.

![Återställ standardvärdet eller ett mer rimligt tröskelvärde](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Följande aviseringar kan tyda på problem med processor resurser:

- PROCESSOR användning för värd (avisering 5)
- Senaste lagrings punkts åtgärd (avisering 28)
- Varaktighet för lagrings punkt (varning 54)

Du kan märka hög CPU-förbrukning i SAP HANA-databasen från något av följande:

- Avisering 5 (processor användning för värd) har Aktiver ATS för aktuell eller tidigare processor användning
- Den visade processor användningen på översikts skärmen

![Visa CPU-användning på översikts skärmen](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Inläsnings diagrammet kan visa hög processor förbrukning eller hög förbrukning tidigare:

![Inläsnings diagrammet kan visa hög processor förbrukning eller hög förbrukning tidigare](./media/troubleshooting-monitoring/image4-load-graph.png)

En avisering som utlöses på grund av hög CPU-användning kan bero på flera orsaker, inklusive, men inte begränsat till: körning av vissa transaktioner, data inläsning, jobb som inte svarar, tids krävande SQL-uttryck och dåliga frågans prestanda (till exempel med BW på HANA-kuber).

Läs mer i avsnittet [SAP HANA fel sökning: CPU-relaterade orsaker och lösningar](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) för detaljerade fel söknings steg.

## <a name="operating-system"></a>Operativsystem

En av de viktigaste kontrollerna för SAP HANA på Linux är att se till att genomskinliga enorma sidor är inaktiverade, se [SAP Obs! #2131662 – transparenta enorma sidor (THP) på SAP HANA-servrar](https://launchpad.support.sap.com/#/notes/2131662).

- Du kan kontrol lera om transparenta enorma sidor är aktiverade genom följande Linux-kommando: **Cat/sys/kernel/mm/transparent \_ hugepage/Enabled**
- Om _alltid_ omges av hakparenteser som nedan, innebär det att de genomskinliga enorma sidorna är aktiverade: [Always] madvise aldrig; Om _aldrig_ omges av hakparenteser som nedan, innebär det att de genomskinliga enorma sidorna är inaktiverade: alltid madvise [Never]

Följande Linux-kommando ska returnera inget: **rpm-frågor och svar | grep-ulimit.** Om den verkar vara installerad _ulimit_ avinstallerar du den omedelbart.

## <a name="memory"></a>Minne

Du kanske upptäcker att mängden minne som allokerats av SAP HANA databasen är högre än förväntat. Följande aviseringar visar problem med hög minnes användning:

- Användning av fysiskt minne för värd (avisering 1)
- Minnes användning för namnserver (avisering 12)
- Total minnes användning för kolumn lagrings tabeller (varning 40)
- Minnes användning för tjänster (varning 43)
- Minnes användning för huvud lagring av kolumn lagrings tabeller (varning 45)
- Dumpfiler för körning av filer (avisering 46)

Mer information om fel söknings steg hittar du på webbplatsen för [SAP HANA fel sökning: minnes problem](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) .

## <a name="network"></a>Nätverk

Se [SAP obs #2081065 – fel sökning SAP HANA nätverk](https://launchpad.support.sap.com/#/notes/2081065) och utföra fel söknings stegen i denna SAP-anteckning.

1. Analysera fördröjnings tiden mellan server och klient.
  A. Kör SQL-skriptet [_Hana- \_ nätverks \_ klienter_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analysera kommunikation mellan noder.
  A. Kör SQL-skript [_Hana \_ nätverks \_ tjänster_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Kör Linux- **ifconfig** (utdata visar om eventuella paket förluster inträffar).
4. Kör Linux-kommandot **tcpdump**.

Använd också [IPERF](https://iperf.fr/) -verktyget med öppen källkod (eller liknande) för att mäta verkliga program nätverks prestanda.

Mer information om fel söknings steg hittar du i webbplatsen [SAP HANA fel sökning: nätverks prestanda och anslutnings problem](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) .

## <a name="storage"></a>Storage

Från ett slut användar perspektiv kör ett program (eller systemet som helhet) sluggishly, svarar inte eller kan även förefalla sluta svara om det uppstår problem med I/O-prestanda. På fliken **volymer** i SAP HANA Studio kan du se de anslutna volymerna och vilka volymer som används av varje tjänst.

![På fliken volymer i SAP HANA Studio kan du se de anslutna volymerna och vilka volymer som används av varje tjänst](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Anslutna volymer i den nedre delen av skärmen du kan se information om volymerna, till exempel filer och I/O-statistik.

![Anslutna volymer i den nedre delen av skärmen du kan se information om volymerna, till exempel filer och I/O-statistik](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Läs mer i [SAP HANA fel sökning: i/O-relaterade Rotors Aker och lösningar](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) och [SAP HANA fel sökning: diskrelaterade platser och lösningar](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) för att se detaljerade fel söknings steg.

## <a name="diagnostic-tools"></a>Diagnostikverktyg

Utför en SAP HANA hälso kontroll via HANA- \_ konfiguration \_ Minichecks. Det här verktyget returnerar potentiella kritiska tekniska problem som redan borde ha höjts som aviseringar i SAP HANA Studio.

Se [SAP Note #1969700-SQL-instruktionen för SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) och hämta SQL Statements.zip-filen som är kopplad till den anteckningen. Lagra denna zip-fil på den lokala hård disken.

I SAP HANA Studio går du till fliken **system information** , högerklickar på kolumnen **namn** och väljer **Importera SQL-uttryck**.

![I SAP HANA Studio går du till fliken system information, högerklickar på kolumnen namn och väljer Importera SQL-uttryck](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Välj den SQL Statements.zip-fil som lagras lokalt och en mapp med motsvarande SQL-uttryck importeras. I det här läget kan många olika diagnostiska kontroller köras med dessa SQL-uttryck.

Om du till exempel vill testa bandbredds kraven för SAP HANA system replikering högerklickar du på **bandbredds** policyn under **replikering: bandbredd** och väljer **Öppna** i SQL-konsolen.

Hela SQL-instruktionen öppnas och gör att indataparametrarna (ändrings avsnittet) kan ändras och sedan köras.

![Hela SQL-instruktionen öppnas och gör att indataparametrarna (ändrings avsnittet) kan ändras och sedan köras](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Ett annat exempel är att högerklicka på instruktionerna under **replikering: översikt**. Välj **Kör** från snabb menyn:

![Ett annat exempel är att högerklicka på instruktionerna under replikering: översikt. Välj Kör från snabb menyn](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Detta ger information som hjälper till med fel sökning:

![Detta leder till information som kan hjälpa till med fel sökning](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Gör samma sak för HANA \_ \_ -Minichecks och kontrol lera om det finns _X_ -markeringar i kolumnen _C_ (kritisk).

Exempel på utdata:

**Hana \_ Konfiguration \_ MiniChecks \_ rev 102.01 + 1** för allmänt SAP HANA kontroller.

![HANA- \_ konfiguration \_ MiniChecks \_ rev 102.01 + 1 för allmänt SAP HANA kontroller](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**Hana \_ \_Översikt över tjänster** för en översikt över vilka SAP HANA-tjänster som körs för närvarande.

![HANA- \_ tjänster \_ – Översikt över vilka SAP HANA-tjänster som körs för närvarande](./media/troubleshooting-monitoring/image12-services-overview.png)

**Hana \_ Tjänste \_ statistik** för SAP HANA tjänst information (CPU, minne osv.).

![HANA \_ Services \_ statistik för SAP HANA tjänst information](./media/troubleshooting-monitoring/image13-services-statistics.png)

**Hana \_ Konfigurations \_ Översikt \_ Rev110 +** för allmän information om SAP HANA-instansen.

![HANA- \_ konfiguration \_ – Översikt \_ Rev110 + för allmän information om SAP HANA-instansen](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**Hana \_ Konfigurations \_ parametrar \_ Rev70 +** för att kontrol lera SAP HANA parametrar.

![HANA \_ konfigurations \_ parametrar \_ Rev70 + för att kontrol lera SAP HANA parametrar](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Nästa steg**

- Se [hur hög tillgänglighet har kon figurer ATS i SUSE med hjälp av STONITH](ha-setup-with-stonith.md).