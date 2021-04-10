---
title: 'Azure-ExpressRoute: Konfigurera NPM för kretsar'
description: Konfigurera molnbaserad nätverks övervakning (NPM) för Azure ExpressRoute-kretsar. Detta omfattar övervakning över ExpressRoute privata peering och Microsoft-peering.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 907c03bd15463368def316e72f55ce214cb3e617
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571042"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurera övervakare av nätverksprestanda för ExpressRoute

Den här artikeln hjälper dig att konfigurera ett Övervakare av nätverksprestanda-tillägg för att övervaka ExpressRoute. Övervakare av nätverksprestanda är en molnbaserad nätverksövervakningslösning som övervakar anslutningen mellan Azures molndistributioner och lokala platser (kontor etc.). Övervakaren av nätverksprestanda är en del av Azure Monitor-loggarna. Övervakning av nätverksprestanda är ett tillägg i ExpressRoute som låter dig övervaka nätverkets prestanda över ExpressRoute-kretsar som är konfigurerade att använda privat peering eller Microsoft-peering. När du konfigurerar övervakning av nätverksprestanda för ExpressRoute, kan du hitta nätverksproblem som ska identifieras och elimineras. Den här tjänsten är också tillgänglig för Azure Government-molnet.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Du kan:

* Övervaka förlust och svars tid i olika virtuella nätverk och Ställ in aviseringar

* Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket

* Felsök tillfälliga och tidpunkts nätverks problem som är svåra att replikera

* Hjälp till att fastställa ett enskilt segment i nätverket som ansvarar för försämrade prestanda

* Hämta data flöde per virtuellt nätverk (om du har agenter installerade i varje VNet)

* Se system tillstånd för ExpressRoute från en tidigare tidpunkt

## <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

Övervaknings agenter installeras på flera servrar, både lokalt och i Azure. Agenterna kommunicerar med varandra, men skickar inte data, de skickar TCP-handskaknings paket. Kommunikationen mellan agenterna gör att Azure kan mappa nätverk sto pol Ogin och sökvägen som trafiken kan ta.

1. Skapa en NPM-arbetsyta. Detta är detsamma som en Log Analytics-arbetsyta.
2. Installera och konfigurera program varu agenter. (Om du bara vill övervaka Microsoft-peering behöver du inte installera och konfigurera program varu agenter.): 
    * Installera övervaknings agenter på lokala servrar och virtuella Azure-datorer (för privat peering).
    * Konfigurera inställningar på övervaknings agent servrarna så att övervaknings agenter kan kommunicera. (Öppna brand Väggs portar osv.)
3. Konfigurera regler för nätverks säkerhets grupper (NSG) så att övervaknings agenten installeras på virtuella Azure-datorer för att kommunicera med lokala övervaknings agenter.
4. Konfigurera övervakning: automatisk identifiering och hantering av vilka nätverk som är synliga i NPM.

Om du redan använder Övervakare av nätverksprestanda för att övervaka andra objekt eller tjänster och du redan har en arbets yta i någon av de regioner som stöds, kan du hoppa över steg 1 och steg 2 och börja konfigurationen med steg 3.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>Steg 1: skapa en arbets yta

Skapa en arbets yta i prenumerationen som har virtuella nätverk-länken till ExpressRoute-kretsen.

1. I [Azure Portal](https://portal.azure.com)väljer du den prenumeration som har virtuella nätverk som peer-kopplats till din ExpressRoute-krets. Sök sedan efter "Övervakare av nätverksprestanda" i listan över tjänster i **Marketplace** . Klicka för att öppna sidan **övervakare av nätverksprestanda** i retur.

   >[!NOTE]
   >Du kan skapa en ny arbets yta eller använda en befintlig arbets yta. Om du vill använda en befintlig arbets yta måste du se till att arbets ytan har migrerats till det nya frågespråket. [Mer information...](../azure-monitor/logs/log-query-overview.md)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Längst ned på huvud **övervakare av nätverksprestanda** sidan klickar du på **skapa** för att öppna sidan **övervakare av nätverksprestanda skapa ny lösning** . Klicka på **Log Analytics arbets yta – Välj en arbets yta** för att öppna sidan arbets ytor. Klicka på **+ Skapa ny arbets yta** för att öppna sidan arbets yta.
3. På sidan **Log Analytics arbets yta** väljer du **Skapa ny** och konfigurerar sedan följande inställningar:

   * Log Analytics arbets yta – ange ett namn för din arbets yta.
   * Prenumeration – om du har flera prenumerationer väljer du den som du vill associera med den nya arbets ytan.
   * Resurs grupp – skapa en resurs grupp eller Använd en befintlig.
   * Plats – den här platsen används för att ange platsen för det lagrings konto som används för agentens anslutnings loggar.
   * Pris nivå – Välj pris nivå.
  
     >[!NOTE]
     >ExpressRoute-kretsen kan finnas var som helst i världen. Den behöver inte finnas i samma region som arbets ytan.
     >
  
     ![arbetsyta](./media/how-to-npm/4.png)<br><br>
4. Klicka på **OK** för att spara och distribuera inställnings mal len. När mallen har validerats klickar du på **skapa** för att distribuera arbets ytan.
5. När arbets ytan har distribuerats navigerar du till den **NetworkMonitoring-resurs (namn)** som du skapade. Verifiera inställningarna och klicka sedan på **lösning kräver ytterligare konfiguration**.

   ![ytterligare konfiguration](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>Steg 2: installera och konfigurera agenter

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2,1: Ladda ned agent installations filen

1. Gå till fliken **gemensamma inställningar** på sidan **övervakare av nätverksprestanda konfiguration** för din resurs. Klicka på den agent som motsvarar serverns processor i avsnittet **installera Log Analytics agenter** och ladda ned installations filen.
2. Kopiera sedan **arbetsyte-ID** och **primär nyckel** till anteckningar.
3. Hämta PowerShell-skriptet från avsnittet **konfigurera Log Analytics agenter för övervakning med TCP-protokoll** . PowerShell-skriptet hjälper dig att öppna relevant brand Väggs port för TCP-transaktionerna.

   ![PowerShell-skript](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2,2: installera en övervaknings agent på varje övervaknings Server (på varje VNET som du vill övervaka)

Vi rekommenderar att du installerar minst två agenter på varje sida av ExpressRoute-anslutningen för redundans (till exempel lokalt, Azure virtuella nätverk). Agenten måste vara installerad på en Windows Server (2008 SP1 eller senare). Det finns inte stöd för att övervaka ExpressRoute-kretsar med Windows Desktop OS och Linux OS. Använd följande steg för att installera agenter:
   
  >[!NOTE]
  >Agenter som har överförts av SCOM (inklusive [MMA](/previous-versions/system-center/system-center-2012-R2/dn465154(v=sc.12))) kanske inte kan upptäcka sin plats konsekvent om de finns i Azure. Vi rekommenderar att du inte använder dessa agenter i Azure virtuella nätverk för att övervaka ExpressRoute.
  >

1. Kör **installations programmet** för att installera agenten på varje server som du vill använda för övervakning av ExpressRoute. Servern som du använder för övervakning kan antingen vara en virtuell dator eller lokalt och måste ha Internet åtkomst. Du måste installera minst en agent lokalt och en agent på varje nätverks segment som du vill övervaka i Azure.
2. På sidan **Välkommen** klickar du på **Nästa**.
3. På sidan **licens villkor** läser du licensen och klickar sedan på **Jag accepterar**.
4. På sidan **målmapp** , ändra eller Behåll standardmappen för installationen och klicka sedan på **Nästa**.
5. På sidan **installations alternativ för agent** kan du välja att ansluta agenten till Azure Monitor loggar eller Operations Manager. Eller så kan du lämna alternativen tomma om du vill konfigurera agenten senare. När du har gjort dina val klickar du på **Nästa**.

   * Om du väljer att ansluta till **Azure Log Analytics** klistrar du in **arbets ytans ID** och den **arbets ytans nyckel** (primär nyckel) som du kopierade i anteckningar i föregående avsnitt. Klicka sedan på **Nästa.**

     ![ID och nyckel](./media/how-to-npm/8.png)
   * Om du väljer att ansluta till **Operations Manager**, på sidan **konfiguration av hanterings grupp** , anger du **hanterings gruppens namn**, **hanterings servern** och **hanterings Server porten**. Klicka sedan på **Nästa.**

     ![Operations Manager](./media/how-to-npm/9.png)
   * På sidan **agent åtgärds konto** väljer du antingen **lokalt system** konto eller  **domän konto eller lokalt dator konto**. Klicka sedan på **Nästa.**

     ![Konto](./media/how-to-npm/10.png)
6. På sidan **klar att installera** granskar du dina val och klickar sedan på **Installera**.
7. På sidan **Konfigurationen har slutförts** klickar du på **Slutför**.
8. När du är klar visas Microsoft Monitoring Agent på kontroll panelen. Du kan granska konfigurationen där och kontrol lera att agenten är ansluten till Azure Monitor loggar. När den är ansluten visar agenten ett meddelande om **att Microsoft Monitoring Agent har anslutit till Microsoft Operations Management Suite tjänsten**.

9. Upprepa proceduren för varje VNET som du behöver övervaka.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2,3: Konfigurera proxyinställningar (valfritt)

Om du använder en webbproxy för att få åtkomst till Internet använder du följande steg för att konfigurera proxyinställningar för Microsoft Monitoring Agent. Utför de här stegen för varje server. Om du har många servrar att konfigurera är det kanske enklare att använda ett skript för att automatisera processen. I så fall, se [Konfigurera proxyinställningar för Microsoft Monitoring Agent med hjälp av ett skript](../azure-monitor/agents/agent-windows.md).

Konfigurera proxyinställningar för Microsoft Monitoring Agent med hjälp av kontroll panelen:

1. Öppna **kontroll panelen**.
2. Öppna **Microsoft Monitoring Agent**.
3. Klicka på fliken **Proxyinställningar**.
4. Välj **Använd en proxyserver** och ange URL och port nummer, om det behövs. Om proxyservern kräver autentisering anger du användarnamn och lösenord för att få åtkomst till proxyservern.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2,4: verifiera agent anslutning

Du kan enkelt kontrol lera om dina agenter kommunicerar.

1. Öppna **kontroll panelen** på en server med övervaknings agenten.
2. Öppna **Microsoft Monitoring Agent**.
3. Klicka på fliken **Azure-Log Analytics** .
4. I kolumnen **status** bör du se att agenten har anslutits till Azure Monitor loggar.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2,5: öppna brand Väggs portarna på Monitoring Agent-servrarna

Om du vill använda TCP-protokollet måste du öppna brand Väggs portar för att säkerställa att övervaknings agenterna kan kommunicera.

Du kan köra ett PowerShell-skript för att skapa de register nycklar som krävs av Övervakare av nätverksprestanda. Det här skriptet skapar också regler för Windows-brandväggen så att övervaknings agenter kan skapa TCP-anslutningar med varandra. Register nycklarna som skapas av skriptet anger om fel söknings loggarna ska loggas och sökvägen till logg filen. Den definierar också den agent-TCP-port som används för kommunikation. Värdena för dessa nycklar anges automatiskt av skriptet. Du bör inte ändra dessa nycklar manuellt.

Port 8084 öppnas som standard. Du kan använda en anpassad port genom att ange parametern port nummer i skriptet. Men om du gör det måste du ange samma port för alla servrar som du kör skriptet på.

>[!NOTE]
>PowerShell-skriptet "EnableRules" konfigurerar endast regler för Windows-brandväggen på den server där skriptet körs. Om du har en nätverks brand vägg bör du kontrol lera att den tillåter trafik som är avsedd för TCP-porten som används av Övervakare av nätverksprestanda.
>
>

Öppna ett PowerShell-fönster med administratörs behörighet på agent servrarna. Kör PowerShell-skriptet [EnableRules](https://aka.ms/npmpowershellscript) (som du laddade ned tidigare). Använd inte några parametrar.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>Steg 3: Konfigurera regler för nätverks säkerhets grupper

Om du vill övervaka agent servrar som finns i Azure måste du konfigurera regler för nätverks säkerhets grupper (NSG) så att TCP-trafik på en port som används av NPM för syntetiska transaktioner. Standard porten är 8084. Detta gör att en övervaknings agent som är installerad på en virtuell Azure-dator kan kommunicera med en lokal övervaknings agent.

Mer information om NSG finns i [nätverks säkerhets grupper](../virtual-network/tutorial-filter-network-traffic.md).

>[!NOTE]
>Kontrol lera att du har installerat agenterna (både den lokala server agenten och Azure Server Agent) och att du har kört PowerShell-skriptet innan du fortsätter med det här steget.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>Steg 4: identifiera peering-anslutningar

1. Gå till panelen för Övervakare av nätverksprestanda översikt genom att gå till sidan **alla resurser** och klicka sedan på arbets ytan allowlisted NPM.

   ![NPM-arbetsyta](./media/how-to-npm/npm.png)
2. Klicka på panelen för **övervakare av nätverksprestanda** översikt för att öppna instrument panelen. Instrument panelen innehåller en ExpressRoute-sida som visar att ExpressRoute är i ett "unconfigureed State". Klicka på **funktions inställningar** för att öppna sidan övervakare av nätverksprestanda konfiguration.

   ![funktions konfiguration](./media/how-to-npm/npm2.png)
3. På sidan konfiguration navigerar du till fliken ExpressRoute peering (peering) på den vänstra sidan. Klicka sedan på **identifiera nu**.

   ![undersöka](./media/how-to-npm/13.png)
4. När identifieringen är klar visas en lista med följande objekt:
   * Alla Microsoft-peering-anslutningar i ExpressRoute-kretsen som är associerade med den här prenumerationen.
   * Alla privata peering-anslutningar som ansluter till virtuella nätverk som är kopplade till den här prenumerationen.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>Steg 5: Konfigurera övervakare

I det här avsnittet konfigurerar du övervakarna. Följ stegen för den typ av peering som du vill övervaka: **privat peering** eller **Microsoft-peering**.

### <a name="private-peering"></a>Privat peering

När identifieringen är klar för privat peering visas regler för unikt **krets namn** och **VNet-namn**. De här reglerna är inaktiverade först.

![regler](./media/how-to-npm/14.png)

1. Markera kryss rutan **övervaka denna peering** .
2. Markera kryss rutan **Aktivera hälso övervakning för denna peering**.
3. Välj övervaknings villkor. Du kan ange anpassade tröskelvärden för att generera hälso händelser genom att ange tröskelvärden. När värdet för villkoret hamnar ovanför det valda tröskelvärdet för det valda nätverket/under nätverks paret genereras en hälso händelse.
4. Klicka på knappen lokal AGENTs **Lägg till agenter** för att lägga till de lokala servrar som du vill övervaka den privata peering-anslutningen från. Se till att du bara väljer agenter som har anslutning till den Microsoft-slutpunkt som du angav i avsnittet för steg 2. Lokala agenter måste kunna komma åt slut punkten med ExpressRoute-anslutningen.
5. Spara inställningarna.
6. När du har aktiverat reglerna och valt de värden och agenter som du vill övervaka, väntar det cirka 30-60 minuter innan värdena börjar fyllas och **ExpressRoute övervaknings** paneler blir tillgängliga.

### <a name="microsoft-peering"></a>Microsoft-peering

För Microsoft-peering, klicka på de Microsoft-peering-anslutningar som du vill övervaka och konfigurera inställningarna.

1. Markera kryss rutan **övervaka denna peering** . 
2. Valfritt Du kan ändra mål slut punkten för Microsoft-tjänsten. Som standard väljer NPM en Microsoft-tjänst slut punkt som mål. NPM övervakar anslutningar från dina lokala servrar till den här mål slut punkten via ExpressRoute. 
    * Om du vill ändra den här mål slut punkten klickar du på länken **(redigera)** under **mål:** och väljer en annan slut punkt för Microsoft-tjänstemål från listan över URL: er.
      ![Redigera mål](./media/how-to-npm/edit_target.png)<br>

    * Du kan använda en anpassad URL eller IP-adress. Det här alternativet är särskilt relevant om du använder Microsoft-peering för att upprätta en anslutning till Azure PaaS-tjänster, till exempel Azure Storage, SQL-databaser och webbplatser som erbjuds på offentliga IP-adresser. Det gör du genom att klicka på länken **(Använd anpassad URL eller IP-adress istället)** längst ned i URL-listan och sedan ange den offentliga slut punkten för din Azure PaaS-tjänst som är ansluten via ExpressRoute Microsoft-peering.
    ![anpassad URL](./media/how-to-npm/custom_url.png)<br>

    * Om du använder dessa valfria inställningar kontrollerar du att endast Microsoft-tjänstens slut punkt är vald här. Slut punkten måste vara ansluten till ExpressRoute och kan kommas åt av de lokala agenterna.
3. Markera kryss rutan **Aktivera hälso övervakning för denna peering**.
4. Välj övervaknings villkor. Du kan ange anpassade tröskelvärden för att generera hälso händelser genom att ange tröskelvärden. När värdet för villkoret hamnar ovanför det valda tröskelvärdet för det valda nätverket/under nätverks paret genereras en hälso händelse.
5. Klicka på knappen lokal AGENTs **Lägg till agenter** för att lägga till de lokala servrar som du vill övervaka Microsoft-peering-anslutningen från. Se till att du bara väljer agenter som har anslutning till de Microsoft-tjänst slut punkter som du angav i avsnittet för steg 2. Lokala agenter måste kunna komma åt slut punkten med ExpressRoute-anslutningen.
6. Spara inställningarna.
7. När du har aktiverat reglerna och valt de värden och agenter som du vill övervaka, väntar det cirka 30-60 minuter innan värdena börjar fyllas och **ExpressRoute övervaknings** paneler blir tillgängliga.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>Steg 6: Visa övervaknings paneler

När du ser övervaknings panelerna övervakas ExpressRoute-kretsarna och anslutnings resurserna av NPM. Du kan klicka på Microsofts peering-panel för att öka detalj nivån för hälsan hos Microsofts peering-anslutningar.

![övervaknings paneler](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Övervakare av nätverksprestanda sida

Sidan NPM innehåller en sida för ExpressRoute som visar en översikt över hälso tillståndet för ExpressRoute-kretsar och peering-kopplingar.

![Skärm bild som visar en instrument panel med en översikt över hälso tillståndet för ExpressRoute-kretsarna och peering-datorerna.](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Lista över kretsar

Om du vill visa en lista över alla övervakade ExpressRoute-kretsar klickar du på panelen **ExpressRoute-kretsar** . Du kan välja en krets och visa dess hälso tillstånd, trend diagram för paket förlust, bandbredds användning och svars tid. Diagrammen är interaktiva. Du kan välja ett anpassat tids fönster för att rita diagram. Du kan dra musen över ett yta i diagrammet för att zooma in och se detaljerade data punkter.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>Trend för förlust, svars tid och data flöde

Diagram över bandbredd, latens och förlust är interaktiva. Du kan zooma in en del av dessa diagram med hjälp av mus kontroller. Du kan också se information om bandbredd, latens och förlust för andra intervall genom att klicka på **datum/tid**, som finns under Åtgärds knappen längst upp till vänster.

![utvecklings](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Peering-lista

Om du vill visa en lista över alla anslutningar till virtuella nätverk via privat peering klickar du på panelen **privata peer** -kopplingar på instrument panelen. Här kan du välja en virtuell nätverks anslutning och visa dess hälso tillstånd, trend diagram för paket förlust, bandbredds användning och svars tid.

![krets lista](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Vyn noder

Om du vill visa en lista över alla länkar mellan lokala noder och virtuella Azure-datorer/Microsoft-tjänstens slut punkter för den valda ExpressRoute peering-anslutningen klickar du på **Visa länkar**. Du kan visa hälso status för varje länk samt trenden för förlust och svars tider som är kopplade till dem.

![vyn noder](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Krets sto pol Ogin

Klicka på panelen **topologi** om du vill visa krets sto pol Ogin. Då går du till topologin för den valda kretsen eller peering. Topology-diagrammet ger svars tiden för varje segment i nätverket. Varje nivå 3-hopp representeras av en nod i diagrammet. Om du klickar på ett hopp visar det mer information om hoppet.

Du kan öka Synlighets nivån så att den inkluderar lokala hopp genom att flytta skjutreglaget under **filter**. Flytta skjutreglaget till vänster eller höger, ökar/minskar antalet hopp i Topology-diagrammet. Svars tiden för varje segment visas, vilket möjliggör snabbare isolering av hög latens segment i nätverket.

![filter](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Detaljerad Topology-vy över en krets

I den här vyn visas VNet-anslutningar.
![detaljerad topologi](./media/how-to-npm/17.png)