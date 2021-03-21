---
title: Anslutnings övervakare i Azure | Microsoft Docs
description: Lär dig hur du använder anslutnings övervakaren för att övervaka nätverkskommunikation i en distribuerad miljö.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 7abaae033d2dbdb329a1f99d8f9845e5965d806c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712325"
---
# <a name="network-connectivity-monitoring-with-connection-monitor"></a>Övervakning av nätverks anslutning med anslutnings övervakaren

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya tester på en befintlig arbets yta eller aktivera en ny arbets yta i Övervakare av nätverksprestanda. Du kommer inte heller att kunna lägga till nya anslutnings övervakare i anslutnings övervakaren (klassisk). Du kan fortsätta att använda de tester och anslutnings Övervakare som skapades före den 1 juli 2021. [Migrera dina tester från övervakare av nätverksprestanda](migrate-to-connection-monitor-from-network-performance-monitor.md) eller [Migrera från anslutnings övervakaren (klassisk)](migrate-to-connection-monitor-from-connection-monitor-classic.md) till den nya anslutnings övervakaren i Azure Network Watcher före den 29 februari 2024 för att minimera avbrott i tjänsten för dina aktuella arbets belastningar.

Anslutnings övervakaren tillhandahåller enhetlig anslutnings övervakning från slut punkt till slut punkt i Azure Network Watcher. Funktionen anslutnings övervakare stöder hybrid-och Azure Cloud-distributioner. Network Watcher innehåller verktyg för att övervaka, diagnostisera och Visa anslutnings relaterade mått för dina Azure-distributioner.

Här är några användnings fall för anslutnings övervakaren:

- Din frontend-webbserver för webb servern kommunicerar med en virtuell databas Server-dator i ett program med flera nivåer. Du vill kontrol lera nätverks anslutningen mellan de två virtuella datorerna.
- Du vill att virtuella datorer i regionen USA, östra, ska pinga virtuella datorer i den centrala regionen och du vill jämföra nätverks fördröjningar i flera regioner.
- Du har flera lokala kontors platser i Seattle, Washington och Ashburn, Virginia. Dina Office-platser ansluter till Microsoft 365 webb adresser. För dina användare av Microsoft 365 webb adresser, jämför fördröjningen mellan Seattle och Ashburn.
- Ditt hybrid program behöver anslutning till en Azure Storage-slutpunkt. Din lokala plats och ditt Azure-program ansluter till samma Azure Storage slut punkt. Du vill jämföra fördröjningen för den lokala platsen med fördröjningen i Azure-programmet.
- Du vill kontrol lera anslutningen mellan dina lokala installationer och de virtuella Azure-datorer som är värdar för moln programmet.

Anslutnings övervakaren kombinerar det bästa av två funktioner: funktionen Network Watcher [anslutnings övervakare (klassisk)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) och tjänsten övervakare av NÄTVERKSPRESTANDA (NPM) [tjänst anslutnings övervakare](../azure-monitor/insights/network-performance-monitor-service-connectivity.md), [ExpressRoute övervakning](../expressroute/how-to-npm.md)och [prestanda övervakning](../azure-monitor/insights/network-performance-monitor-performance-monitor.md) .

Här följer några fördelar med anslutnings övervakaren:

* Enhetlig, intuitiv upplevelse för Azure och hybrid övervaknings behov
* Övervakning över flera regioner och anslutningar mellan arbets ytor
* Högre avsöknings frekvens och bättre insyn i nätverks prestanda
* Snabbare aviseringar för dina hybrid distributioner
* Stöd för anslutnings kontroller som baseras på HTTP, TCP och ICMP 
* Mått och Log Analytics stöd för både Azure-test och icke-Azure-testkonfigurationer

![Diagram över hur anslutnings övervakaren interagerar med virtuella Azure-datorer, icke-Azure-värdar, slut punkter och data lagrings platser](./media/connection-monitor-2-preview/hero-graphic.png)

Följ dessa steg om du vill börja använda anslutnings övervakaren för övervakning: 

1. Installera övervaknings agenter.
1. Aktivera Network Watcher på din prenumeration.
1. Skapa en anslutnings övervakare.
1. Konfigurera data analys och aviseringar.
1. Diagnostisera problem i nätverket.

I följande avsnitt finns information om de här stegen.

## <a name="install-monitoring-agents"></a>Installera övervaknings agenter

Anslutnings övervakaren förlitar sig på enkla körbara filer för att köra anslutnings kontroller. Det stöder anslutnings kontroller från både Azure-miljöer och lokala miljöer. Vilken körbar fil du använder beror på om din virtuella dator finns på Azure eller lokalt.

### <a name="agents-for-azure-virtual-machines"></a>Agenter för virtuella Azure-datorer

Om du vill att anslutnings övervakaren ska identifiera dina virtuella Azure-datorer som övervaknings källor installerar du tillägget Network Watcher agent virtuell dator. Tillägget kallas även *Network Watcher-tillägget*. Virtuella Azure-datorer kräver tillägget för att utlösa övervakning från slut punkt till slut punkt och andra avancerade funktioner. 

Du kan installera Network Watcher-tillägget när du [skapar en virtuell dator](./connection-monitor.md#create-the-first-vm). Du kan också separat installera, konfigurera och felsöka Network Watcher-tillägget för [Linux](../virtual-machines/extensions/network-watcher-linux.md) och [Windows](../virtual-machines/extensions/network-watcher-windows.md).

Regler för en nätverks säkerhets grupp (NSG) eller brand vägg kan blockera kommunikation mellan källan och målet. Anslutnings övervakaren identifierar det här problemet och visar det som ett diagnostiskt meddelande i topologin. Om du vill aktivera anslutnings övervakning kontrollerar du att NSG-och brand Väggs reglerna tillåter paket över TCP eller ICMP mellan källan och målet.

### <a name="agents-for-on-premises-machines"></a>Agenter för lokala datorer

Om du vill att anslutnings övervakaren ska identifiera dina lokala datorer som källor för övervakning installerar du Log Analytics agent på datorerna.  Aktivera sedan Övervakare av nätverksprestanda-lösningen. Dessa agenter är länkade till Log Analytics arbets ytor, så du måste konfigurera arbetsyte-ID och primär nyckel innan agenterna kan börja övervaka.

För att installera Log Analytics agent för Windows-datorer, se [Azure Monitor tillägg för virtuell dator för Windows](../virtual-machines/extensions/oms-windows.md).

Om sökvägen omfattar brand väggar eller virtuella nätverks installationer (NVA) kontrollerar du att målet kan kontaktas.

För att öppna porten för Windows-datorer kör du [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell-skriptet utan parametrar i ett PowerShell-fönster med administratörs behörighet.

PortNumbers som ska användas måste ändras manuellt för Linux-datorer. 
* Navigera till sökvägen:/var/opt/Microsoft/omsagent/npm_state. 
* Öppna fil: npmdregistry
* Ändra värdet för port nummer ```“PortNumber:<port of your choice>”```

 Observera att port nummer som används ska vara samma för alla agenter som används i en arbets yta. 

Skriptet skapar register nycklar som krävs av lösningen. Det skapar också regler för Windows-brandväggen för att tillåta agenter att skapa TCP-anslutningar med varandra. Register nycklarna som skapas av skriptet anger om fel söknings loggarna och sökvägen till logg filen ska loggas. Skriptet definierar också den agent-TCP-port som används för kommunikation. Värdena för dessa nycklar anges automatiskt av skriptet. Ändra inte nycklarna manuellt. Porten som öppnas som standard är 8084. Du kan använda en anpassad port genom att ange parametern port nummer i skriptet. Använd samma port på alla datorer där skriptet körs. [Läs mer](../azure-monitor/agents/log-analytics-agent.md#network-requirements) om nätverks kraven för Log Analytics agenter

Skriptet konfigurerar endast Windows-brandväggen lokalt. Om du har en nätverks brand vägg kontrollerar du att den tillåter trafik som är avsedd för TCP-porten som används av Övervakare av nätverksprestanda.

## <a name="enable-network-watcher-on-your-subscription"></a>Aktivera Network Watcher på din prenumeration

Alla prenumerationer som har ett virtuellt nätverk har Aktiver ATS med Network Watcher. När du skapar ett virtuellt nätverk i din prenumeration aktive ras Network Watcher automatiskt i det virtuella nätverkets region och prenumeration. Den här automatiska aktiveringen påverkar inte dina resurser eller debiteras. Se till att Network Watcher inte uttryckligen har inaktiverats för din prenumeration. 

Mer information finns i [aktivera Network Watcher](./network-watcher-create.md).

## <a name="create-a-connection-monitor"></a>Skapa en anslutningsövervakare 

Anslutnings övervakaren övervakar kommunikation med jämna mellanrum. Du informerar om ändringar av tillgängligheten och svars tiden. Du kan också kontrol lera den aktuella och historiska nätverks sto pol Ogin mellan käll agenter och slut punkter för mål.

Källor kan vara virtuella Azure-datorer eller lokala datorer som har en installerad övervaknings agent. Mål slut punkter kan vara Microsoft 365 webb adresser, Dynamics 365-URL: er, anpassade URL: er, resurs-ID: n för virtuella Azure-datorer, IPv4, IPv6, FQDN eller domän namn.

### <a name="access-connection-monitor"></a>Övervakaren åtkomst anslutning

1. På Azure Portal start sida går du till **Network Watcher**.
1. Till vänster i avsnittet **övervakning** väljer du **anslutnings övervakare**.
1. Du ser alla anslutnings Övervakare som skapades i anslutnings övervakaren. Om du vill se de anslutnings Övervakare som skapades i den klassiska upplevelsen av anslutnings övervakaren går du till fliken **anslutnings övervakare** .
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Skärm bild som visar anslutnings Övervakare som har skapats i anslutnings övervakaren" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Skapa en anslutningsövervakare

I anslutnings Övervakare som du skapar i anslutnings övervakaren kan du lägga till både lokala datorer och virtuella Azure-datorer som källor. Dessa anslutnings övervakare kan också övervaka anslutningar till slut punkter. Slut punkterna kan vara på Azure eller någon annan URL eller IP-adress.

Anslutnings övervakaren innehåller följande entiteter:

* **Anslutnings övervaknings resurs** – en regions-/regionsspecifika Azure-resurs. Alla följande entiteter är egenskaper för en anslutnings övervaknings resurs.
* **Slut punkt** – en källa eller ett mål som deltar i anslutnings kontroller. Exempel på slut punkter är virtuella Azure-datorer, lokala agenter, URL: er och IP-adresser.
* **Test konfiguration** – en plattformsspecifik konfiguration för ett test. Utifrån det protokoll du väljer kan du definiera port, tröskelvärden, test frekvens och andra parametrar.
* **Test grupp** – gruppen som innehåller käll slut punkter, mål slut punkter och testkonfigurationer. En anslutnings övervakare kan innehålla fler än en test grupp.
* **Test** – kombinationen av en käll slut punkt, mål slut punkt och test konfiguration. Ett test är den mest detaljerade nivån där övervaknings data är tillgängliga. Övervaknings data innehåller procent andelen kontroller som misslyckades och tur och retur-tiden.

 ![Diagram som visar en anslutnings Övervakare som definierar relationen mellan test grupper och tester](./media/connection-monitor-2-preview/cm-tg-2.png)

Du kan skapa en anslutnings övervakare med hjälp av [Azure Portal](./connection-monitor-create-using-portal.md), [ARMClient](./connection-monitor-create-using-template.md) eller [PowerShell](connection-monitor-create-using-powershell.md)

Alla källor, destinationer och testkonfigurationer som du lägger till i en test grupp får delats upp till enskilda tester. Här är ett exempel på hur källor och mål är uppdelade:

* Test grupp: TG1
* Källor: 3 (A, B, C)
* Mål: 2 (D, E)
* Testa konfigurationer: 2 (Konfig 1, konfig 2)
* Totalt antal tester som skapats: 12

| Test nummer | Källa | Mål | Testa konfiguration |
| --- | --- | --- | --- |
| 1 | A | D | Konfiguration 1 |
| 2 | A | D | Konfig 2 |
| 3 | A | E | Konfiguration 1 |
| 4 | A | E | Konfig 2 |
| 5 | B | D | Konfiguration 1 |
| 6 | B | D | Konfig 2 |
| 7 | B | E | Konfiguration 1 |
| 8 | B | E | Konfig 2 |
| 9 | C | D | Konfiguration 1 |
| 10 | C | D | Konfig 2 |
| 11 | C | E | Konfiguration 1 |
| 12 | C | E | Konfig 2 |

### <a name="scale-limits"></a>Skalnings gränser

Anslutnings övervakare har följande skalnings gränser:

* Högsta antal anslutnings övervakare per prenumeration per region: 100
* Högsta antal test grupper per anslutnings Övervakare: 20
* Högsta antal källor och mål per anslutnings Övervakare: 100
* Högsta antal testkonfigurationer per anslutnings Övervakare: 20

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analysera övervaknings data och ange aviseringar

När du har skapat en anslutnings övervakare kontrollerar källorna anslutningen till destinationer baserat på din test konfiguration.

### <a name="checks-in-a-test"></a>Checkar in ett test

Baserat på det protokoll som du valde i test konfigurationen kör anslutnings övervakaren en serie kontroller för käll mål paret. Kontrollerna körs enligt den test frekvens som du har valt.

Om du använder HTTP beräknar tjänsten antalet HTTP-svar som returnerade en giltig svarskod. Giltiga svars koder kan anges med PowerShell och CLI. Resultatet bestämmer procent andelen misslyckade kontroller. För att kunna beräkna en sökperiod mäter tjänsten tiden mellan ett HTTP-anrop och svaret.

Om du använder TCP eller ICMP beräknar tjänsten paket förlusten i procent för att avgöra procent andelen misslyckade kontroller. Tjänsten mäter den tid det tar att ta emot bekräftelsen (ACK) för de paket som har skickats för att beräkna den. Om du har aktiverat traceroute-data för dina nätverks test kan du se förlust och svars tid för hopp för ditt lokala nätverk.

### <a name="states-of-a-test"></a>Tillstånd för ett test

Beroende på vilka data som kontrollerna returnerar kan testerna ha följande tillstånd:

* **Pass** – faktiska värden för procent andelen misslyckade kontroller och efter frågan inom de angivna tröskelvärdena.
* **Misslyckades** – faktiska värden för procent andelen misslyckade kontroller eller söksöker överskred de angivna tröskelvärdena. Om inget tröskelvärde anges, kommer ett test att nå fel tillstånd när procent andelen misslyckade kontroller är 100.
* **Varning** – 
     * Om tröskelvärdet har angetts och anslutnings övervakaren söker efter misslyckade procent över 80% av tröskelvärdet markeras testet som varning.
     * I avsaknad av angivna tröskelvärden tilldelar anslutnings övervakaren automatiskt ett tröskelvärde. När tröskelvärdet överskrids ändras test statusen till varning.För tur och retur-tid i TCP-eller ICMP-test är tröskelvärdet 750msec. För kontrollerna misslyckades procent är tröskelvärdet 10%. 
* **Obestämd**   – Inga data i Log Analytics-arbetsytan.Kontrol lera mått. 
* **Körs inte**   – Inaktiverat genom att inaktivera test gruppen  

### <a name="data-collection-analysis-and-alerts"></a>Data insamling, analys och aviseringar

De data som anslutnings övervakaren samlar in lagras i arbets ytan Log Analytics. Du konfigurerar den här arbets ytan när du skapade anslutnings övervakaren. 

Övervaknings data finns också i Azure Monitor Mät värden. Du kan använda Log Analytics för att behålla dina övervaknings data så länge du vill. Azure Monitor lagrar mått för endast 30 dagar som standard. 

Du kan [ställa in Metric-baserade aviseringar för data](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Övervaka instrument paneler

På instrument panelen för övervakning visas en lista över de anslutnings Övervakare som du har åtkomst till för dina prenumerationer, regioner, tidsstämplar, källor och mål typer.

När du går till anslutnings övervakaren från Network Watcher kan du visa data genom att:

* **Anslutnings övervakare** – lista över alla anslutnings Övervakare som skapats för prenumerationer, regioner, tidsstämplar, källor och mål typer. Den här vyn är standard.
* **Test grupper** – lista över alla test grupper som har skapats för dina prenumerationer, regioner, tidsstämplar, källor och mål typer. Dessa test grupper filtreras inte efter anslutnings övervakare.
* **Test** – lista över alla tester som körs för dina prenumerationer, regioner, tidsstämplar, källor och mål typer. Dessa tester filtreras inte efter anslutnings övervakare eller test grupper.

I följande bild anges de tre datavyerna efter pil 1.

På instrument panelen kan du expandera varje anslutnings Övervakare för att se dess test grupper. Sedan kan du expandera varje test grupp för att se de tester som körs i den. 

Du kan filtrera en lista baserat på:

* **Filter på högsta nivån** – Sök i listan efter text, entitetstyp (anslutnings övervakare, test grupp eller test) tidsstämpel och omfång. Omfattningen omfattar prenumerationer, regioner, källor och mål typer. Se fält 1 i följande bild.
* **Tillstånds baserade filter** – filtrera efter tillstånd för anslutnings övervakaren, test gruppen eller testet. Se ruta 2 i följande bild.
* **Varnings baserat filter** – filtrera efter aviseringar som utlösts på anslutnings övervaknings resursen. Se fält 3 i följande bild.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="Skärm bild som visar hur du filtrerar vyer för anslutnings övervakare, test grupper och tester i anslutnings övervakaren " lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Om du till exempel vill titta på alla tester i anslutnings övervakaren där käll-IP: en är 10.192.64.56:
1. Ändra vyn för att **testa**.
1. I Sök fältet skriver du *10.192.64.56*
1. I **omfång** i högsta nivå filter väljer du **källor**.

Om du bara vill visa misslyckade tester i anslutnings övervakaren där käll-IP: en är 10.192.64.56:
1. Ändra vyn för att **testa**.
1. För det tillståndbaserade filtret väljer du **fungerar inte**.
1. I Sök fältet skriver du *10.192.64.56*
1. I **omfång** i högsta nivå filter väljer du **källor**.

Visa endast misslyckade tester i anslutnings övervakaren där målet är outlook.office365.com:
1. Ändra vy att **testa**.
1. För det tillståndbaserade filtret väljer du **fungerar inte**.
1. Skriv *Office.live.com* i Sök fältet
1. I **omfång** i filtret på högsta nivån väljer du **destinationer**.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Skärm bild som visar en vy som är filtrerad för att endast Visa misslyckade tester för Outlook.Office365.com-målet" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Om du vill veta orsaken till att en anslutnings övervakare eller test grupp eller ett test har misslyckats klickar du på kolumn namnet orsak.  Detta anger vilket tröskelvärde (kontroller som misslyckats% eller sökpost) har brutits och relaterade diagnostikloggar
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="Skärm bild som visar orsaken till ett problem med anslutnings övervakaren, test-eller test gruppen" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
Visa trender i sökrutan och procent andelen misslyckade kontroller för en anslutnings Övervakare:
1. Välj den anslutnings Övervakare som du vill undersöka.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="Skärm bild som visar mått för en anslutnings Övervakare som visas av test gruppen" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. Följande avsnitt visas  
    1. Essentials-resurs egenskaper för den valda anslutnings övervakaren 
    1. Drag 
        1. Sammanställda trend linjer för söksöka och procent andel misslyckade kontroller för alla tester i anslutnings övervakaren. Du kan ange en angiven tid för att visa information.
        1. Topp 5 i test grupper, källor och mål baserat på efter fråge-eller procent andel misslyckade kontroller. 
    1. Flikar för test grupper, källor, destinationer och testkonfigurationer – visar en lista över test grupper, källor eller mål i anslutnings övervakaren. Det gick inte att kontrol lera efter fel, det gick inte att lägga till mängd sökvärden  Du kan också gå tillbaka i tid för att visa data. 
    1. Problem – problem med hopp nivån för varje test i anslutnings övervakaren. 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="Skärm bild som visar mått för en anslutnings Övervakare som visas av test grupp del 2" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. Du kan
    * Klicka på Visa alla tester-om du vill visa alla tester i anslutnings övervakaren
    * Klicka på Visa alla test grupper, testa konfigurationer, källor och destinationer – om du vill visa information som är speciell för var och en. 
    * Välj en test grupp, testa konfiguration, källa eller mål – för att visa alla tester i entiteten.

1. Från vyn Visa alla tester kan du:
    * Välj test och klicka på Jämför.
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="Skärm bild som visar jämförelse av 2 tester" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * Använd kluster för att expandera sammansatta resurser som VNET, undernät till dess underordnade resurser
    * Visa topologi för alla tester genom att klicka på topologi.

För att visa trender i sökrutan och procent andelen misslyckade kontroller för en test grupp:
1. Välj den test grupp som du vill undersöka. 
1. Du ser liknande anslutnings övervakare – Essentials, Summary, Table for test Groups, sources, destinationer och testkonfigurationerna. Navigera till dem som du gör för en anslutnings övervakare

För att visa trender i sökrutan och procent andelen misslyckade kontroller för ett test:
1. Välj det test som du vill undersöka. Du ser nätverk sto pol Ogin och slut punkt till slut punkt trend diagram för kontroller som misslyckats% och tid för tur och retur. Om du vill se de identifierade problemen i topologin väljer du ett hopp i sökvägen. (Dessa hopp är Azure-resurser.) Den här funktionen är för närvarande inte tillgänglig för lokala nätverk

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Skärm bild som visar en Topology-vy för ett test" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Logg frågor i Log Analytics

Använd Log Analytics för att skapa anpassade vyer för dina övervaknings data. Alla data som visas i användar gränssnittet är från Log Analytics. Du kan analysera data interaktivt i lagrings platsen. Korrelera data från Agenthälsa eller andra lösningar som baseras på Log Analytics. Exportera data till Excel eller Power BI eller skapa en delnings bar länk.

#### <a name="metrics-in-azure-monitor"></a>Mått i Azure Monitor

I anslutnings Övervakare som skapades före anslutnings övervakaren, är alla fyra mått tillgängliga:% avsökningar misslyckades, AverageRoundtripMs, ChecksFailedPercent (för hands version) och RoundTripTimeMs (för hands version). I anslutnings Övervakare som skapades i anslutnings övervakaren är data bara tillgängliga för mått som är taggade med *(för hands version)*.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="Skärm bild som visar mått i anslutnings övervakaren" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

När du använder mått anger du resurs typen som Microsoft. Network/networkWatchers/connectionMonitors

| Metric | Visningsnamn | Enhet | Sammansättningstyp | Beskrivning | Dimensioner |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent (klassisk) | % Avsökningar misslyckades (klassisk) | Procent | Genomsnitt | Procent av anslutnings övervaknings avsökningarna misslyckades. | Inga dimensioner |
| AverageRoundtripMs (klassisk) | Genomsnittlig tid för tur och retur (MS) (klassisk) | Millisekunder | Genomsnitt | Genomsnittlig för inblandning av nätverks belastning för anslutnings övervaknings avsökningar skickas mellan källa och mål. |             Inga dimensioner |
| ChecksFailedPercent | % Kontroller misslyckades | Procent | Genomsnitt | Procent andelen misslyckade kontroller för ett test. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protokoll <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region |
| RoundTripTimeMs | Svars tid för fördröjning (MS) | Millisekunder | Genomsnitt | Söker efter kontroller som skickats mellan källa och mål. Värdet är inte medelvärdet. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protokoll <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region |
| TestResult | Test resultat | Antal | Genomsnitt | Test resultat för anslutnings övervakaren | SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protokoll <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Metric-baserade aviseringar för anslutnings övervakaren

Du kan skapa mått aviseringar för anslutnings övervakare med hjälp av metoderna nedan 

1. Från anslutnings övervakaren vid skapande av anslutnings övervakare [med hjälp av Azure Portal](connection-monitor-preview-create-using-portal.md#) 
1. Från anslutnings övervakaren med "Konfigurera aviseringar" i instrument panelen 
1. Från Azure Monitor – för att skapa en avisering i Azure Monitor: 
    1. Välj den anslutnings övervaknings resurs som du skapade i anslutnings övervakaren.
    1. Se till att **måttet** visas som signal typ för anslutnings övervakaren.
    1. I **Lägg till villkor**, för **signal namnet**, väljer du **ChecksFailedPercent (för hands version)** eller **RoundTripTimeMs (för hands version)**.
    1. Välj **mått** för **signal typ**. Välj till exempel **ChecksFailedPercent (för hands version)**.
    1. Alla mått för måttet visas. Välj dimensions namn och dimensions värde. Välj till exempel **käll adress** och ange IP-adressen för vilken källa som helst i anslutnings övervakaren.
    1. Fyll i följande information i **aviserings logik**:
        * **Villkors typ**: **statisk**.
        * **Villkor** och **tröskel**.
        * **Sammansättnings precision och utvärderings frekvens**: anslutnings övervakaren uppdaterar data varje minut.
    1. I **åtgärder** väljer du din åtgärds grupp.
    1. Ange aviserings information.
    1. Skapa varnings regeln.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Skärm bild som visar avsnittet Skapa regel i Azure Monitor. Käll adressen och källans slut punkts namn har marker ATS" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>Diagnostisera problem i nätverket

Anslutnings övervakaren hjälper dig att diagnostisera problem i anslutnings övervakaren och nätverket. Problem i ditt hybrid nätverk identifieras av de Log Analyticss agenter som du installerade tidigare. Problem i Azure identifieras av Network Watcher-tillägget. 

Du kan visa problem i Azure-nätverket i nätverk sto pol Ogin.

Följande problem kan identifieras för nätverk vars källor är lokala virtuella datorer:

* Tids gränsen nåddes för begäran.
* Slut punkten har inte lösts av DNS – temporär eller beständig. URL är ogiltig.
* Inga värdar hittades.
* Källan kan inte ansluta till målet. Målet kan inte nås via ICMP.
* Certifikatrelaterade problem: 
    * Klient certifikat som krävs för att autentisera agenten. 
    * Listan över återkallade certifikat är inte tillgänglig. 
    * Värd namnet för slut punkten stämmer inte överens med certifikatets ämne eller alternativa namn för certifikat mottagare. 
    * Rot certifikatet saknas i källans Arkiv för betrodda certifikat utfärdare. 
    * SSL-certifikatet har upphört att gälla, är ogiltigt, återkallat eller inkompatibelt.

Följande problem kan identifieras för nätverk vars källor är virtuella Azure-datorer:

* Agent problem:
    * Agenten stoppades.
    * DNS-matchning misslyckades.
    * Inget program eller lyssnare lyssnar på mål porten.
    * Det gick inte att öppna socketen.
* Problem med VM-tillstånd: 
    * Startar
    * Stoppas
    * Stoppad
    * Frigör
    * Frigjord
    * Starta om
    * Inte allokerat
* ARP-tabell posten saknas.
* Trafiken blockerades på grund av problem med lokala brand väggar eller NSG-regler.
* Problem med virtuell nätverksgateway: 
    * Vägar som saknas.
    * Tunneln mellan två gateways är frånkopplad eller saknas.
    * Den andra gatewayen kunde inte hittas av tunneln.
    * Ingen peering-information hittades.
* Vägen saknades i Microsoft Edge.
* Trafiken stoppades på grund av system vägar eller UDR.
* BGP är inte aktiverat på Gateway-anslutningen.
* DIP-avsökningen är nere i belastningsutjämnaren.

## <a name="next-steps"></a>Nästa steg
    
   * Lär dig [hur du skapar anslutnings övervakare med hjälp av Azure Portal](./connection-monitor-create-using-portal.md)  
   * Lär dig [hur du skapar anslutnings övervakaren med ARMClient](./connection-monitor-create-using-template.md)