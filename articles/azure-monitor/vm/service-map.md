---
title: Använda Tjänstkarta lösning i Azure | Microsoft Docs
description: Tjänstkarta är en lösning i Azure som automatiskt identifierar programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Den här artikeln innehåller information om hur du distribuerar Tjänstkarta i din miljö och använder den i flera olika scenarier.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2019
ms.openlocfilehash: 0374c7d304a40031919bae8816d31477e742b9c8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624233"
---
# <a name="using-service-map-solution-in-azure"></a>Använda lösningen Tjänstkarta i Azure

Tjänstkarta identifierar automatiskt programkomponenter i Windows- och Linux-system och mappar kommunikationen mellan olika tjänster. Med Tjänstkarta kan du se dina servrar på samma sätt som du tänker på dem, dvs. som sammankopplade system som levererar kritiska tjänster. Tjänstkarta visar anslutningar mellan servrar, processer, inkommande och utgående anslutningssvarstid, samt portar i valfri TCP-ansluten arkitektur, utan att det krävs någon konfiguration förutom installationen av en agent.

I den här artikeln beskrivs hur du onboarding och använder Tjänstkarta. Kraven för lösningen är följande:

* En Log Analytics arbets yta i en [region som stöds](vminsights-configure-workspace.md#supported-regions).

* [Log Analytics-agenten](vminsights-enable-overview.md#agents) som är installerad på Windows-datorn eller Linux-servern ansluten till samma arbets yta som du aktiverade lösningen med.

* [Beroende agenten](vminsights-enable-overview.md#agents) som är installerad på Windows-datorn eller Linux-servern.

>[!NOTE]
>Om du redan har distribuerat Tjänstkarta kan du nu även visa dina kartor i Azure Monitor for VMs, som innehåller ytterligare funktioner för att övervaka hälso tillstånd och prestanda för virtuella datorer. Läs mer i [Azure Monitor for VMS översikt](../vm/vminsights-overview.md). Mer information om skillnaderna mellan Tjänstkarta lösning och Azure Monitor for VMs kart funktion finns i följande [vanliga frågor och svar](../faq.md#azure-monitor-for-vms).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Aktivera Tjänstkarta

1. Aktivera Tjänstkarta-lösningen från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) eller genom att använda processen som beskrivs i [Lägg till övervaknings lösningar från Lösningsgalleriet](../insights/solutions.md).
1. [Installera beroende agenten på Windows](../vm/vminsights-enable-hybrid.md#install-the-dependency-agent-on-windows) eller [Installera beroende agenten i Linux](../vm/vminsights-enable-hybrid.md#install-the-dependency-agent-on-linux) på varje dator där du vill hämta data. Beroendeagenten kan övervaka anslutningar till omedelbara grannar, så du behöver kanske inte ha en agent på varje dator.

Du kommer åt Tjänstkarta i Azure Portal från arbets ytan Log Analytics och väljer alternativ **lösningar** i den vänstra rutan.<br><br> ![Alternativet Välj lösningar på arbets ytan ](./media/service-map/select-solution-from-workspace.png) .<br> I listan med lösningar väljer du **ServiceMap (workspaceName)** och på översikts sidan för tjänstkarta lösning klickar du på panelen sammanfattning av tjänstkarta.<br><br> ![Panelen Tjänstkarta Sammanfattning ](./media/service-map/service-map-summary-tile.png) .

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Användnings fall: gör din IT-process beroende medveten

### <a name="discovery"></a>Identifiering

Tjänstkarta skapar automatiskt en gemensam referens karta över beroenden för dina servrar, processer och tjänster från tredje part. Den identifierar och mappar alla TCP-beroenden, identifierar överrasknings anslutningar, fjärranslutna system från tredje part som du är beroende av och beroenden till traditionella mörka delar av nätverket, till exempel Active Directory. Tjänstkarta identifierar misslyckade nätverks anslutningar som hanteras av dina hanterade system, vilket hjälper dig att identifiera potentiell Server fel konfiguration, avbrott i tjänsten och nätverks problem.

### <a name="incident-management"></a>Incidenthantering

Tjänstkarta hjälper till att undvika att isolera problem genom att visa hur systemen är anslutna och påverkar varandra. Förutom att identifiera misslyckade anslutningar hjälper det till att identifiera felkonfigurerade belastningsutjämnare, överraskande eller överdriven belastning på kritiska tjänster och falska klienter, till exempel utvecklare som utvecklar datorer som kommunicerar med produktions systemen. Genom att använda integrerade arbets flöden med Ändringsspårning kan du också se om en ändrings händelse på en backend-dator eller-tjänst förklarar rotor saken till en incident.

### <a name="migration-assurance"></a>Migration Assurance

Genom att använda Tjänstkarta kan du effektivt planera, påskynda och validera Azure-migreringar, vilket hjälper till att se till att inget är kvar bakom och oväntade avbrott sker inte. Du kan identifiera alla beroende system som behöver migreras tillsammans, utvärdera systemets konfiguration och kapacitet och identifiera om ett system som är igång fortfarande betjänar användare eller är en kandidat för avställning i stället för migrering. När flyttningen är klar kan du kontrol lera klient belastning och identitet för att kontrol lera att test system och kunder ansluter. Om under nätets planering och brand Väggs definitioner har problem, misslyckade anslutningar i Tjänstkarta Maps, pekar du på de system som behöver anslutning.

### <a name="business-continuity"></a>Verksamhetskontinuitet

Om du använder Azure Site Recovery och behöver hjälp med att definiera återställnings ordningen för din program miljö kan Tjänstkarta automatiskt visa hur systemen förlitar sig på varandra för att säkerställa att din återställnings plan är tillförlitlig. Genom att välja en kritisk Server eller grupp och visa dess klienter kan du identifiera vilka klient dels system som ska återställas efter att servern har återställts och är tillgänglig. Genom att titta på kritiska servrars backend-beroenden kan du identifiera vilka system som ska återställas innan dina fokus system återställs.

### <a name="patch-management"></a>Uppdateringshantering

Tjänstkarta förbättrar din användning av system uppdaterings utvärderingen genom att visa vilka andra team och servrar som är beroende av tjänsten, så att du kan meddela dem i förväg innan du tar bort dina system för korrigering. Tjänstkarta förbättrar också korrigerings hanteringen genom att visa om dina tjänster är tillgängliga och korrekt anslutna efter att de har korrigerats och startats om.

## <a name="mapping-overview"></a>Översikt över mappning

Tjänstkarta agenter samlar in information om alla TCP-anslutna processer på den server där de är installerade och information om inkommande och utgående anslutningar för varje process.

I listan i det vänstra fönstret kan du välja datorer eller grupper som har Tjänstkarta agenter för att visualisera deras beroenden under ett visst tidsintervall. Dator beroende mappar fokuserar på en angiven dator och visar alla datorer som är direkt TCP-klienter eller servrar på den datorn.  Dator grupps kartor visar uppsättningar av servrar och deras beroenden.

![Översikt över Tjänstkarta](media/service-map/service-map-overview.png)

Datorer kan expanderas i kartan för att visa de process grupper och processer som körs med aktiva nätverks anslutningar under det valda tidsintervallet. När en fjärrdator med en Tjänstkarta-agent expanderas för att Visa process information visas bara de processer som kommunicerar med fokus datorn. Antalet klient datorer som körs utan agent och som ansluter till fokus datorn visas till vänster om de processer som de ansluter till. Om fokus datorn gör en anslutning till en backend-dator som inte har någon agent, ingår backend-servern i en server port grupp, tillsammans med andra anslutningar till samma port nummer.

Som standard visar Tjänstkarta Maps de senaste 30 minuterna beroende information. Genom att använda tids kontrollerna längst upp till vänster kan du fråga kartor efter historiska tidsintervall på upp till en timme för att visa hur beroenden som tittat tidigare (till exempel under en incident eller innan en ändring inträffade). Tjänstkarta data lagras i 30 dagar i betalda arbets ytor och i 7 dagar i kostnads fria arbets ytor.

## <a name="status-badges-and-border-coloring"></a>Status skylter och kant linje färgning

Längst ned på varje server i kartan kan vara en lista över status skyltar som förmedlar statusinformation om servern. Aktivitets ikonerna anger att det finns viss relevant information för servern från en av lösnings integreringarna. Om du klickar på en flagga tas du direkt till information om statusen i den högra rutan. De status märken som är tillgängliga för närvarande är aviseringar, Service Desk, ändringar, säkerhet och uppdateringar.

Beroende på status märkenas allvarlighets grad kan datorns Node-kantlinjes vara röd (kritisk), gul (varning) eller blå (information). Färgen representerar de mest allvarliga statusvärdena för alla status märken. En grå kant linje visar en nod som inte har några status indikatorer.

![Status skyltar](media/service-map/status-badges.png)

## <a name="process-groups"></a>Process grupper

Process grupper kombinerar processer som associeras med en gemensam produkt eller tjänst till en process grupp.  När en datornod expanderas visas fristående processer tillsammans med process grupper.  Om inkommande och utgående anslutningar till en process i en process grupp har misslyckats visas anslutningen som misslyckad för hela process gruppen.

## <a name="machine-groups"></a>Datorgrupper

Med dator grupper kan du se kartor som är centrerade runt en uppsättning servrar, inte bara en så att du kan se alla medlemmar i ett program i flera nivåer eller ett Server kluster på en karta.

Användare väljer vilka servrar som ska ingå i en grupp och väljer ett namn för gruppen.  Du kan sedan välja att Visa gruppen med alla dess processer och anslutningar, eller Visa den med bara de processer och anslutningar som är direkt relaterade till de andra medlemmarna i gruppen.

![Dator grupp](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Skapa en dator grupp

Om du vill skapa en grupp väljer du den dator eller de datorer som du vill använda i listan datorer och klickar på **Lägg till i grupp**.

![Skapa grupp](media/service-map/machine-groups-create.png)

Där kan du välja **Skapa ny** och ge gruppen ett namn.

![Namn grupp](media/service-map/machine-groups-name.png)

>[!NOTE]
>Dator grupper är begränsade till 10 servrar.

### <a name="viewing-a-group"></a>Visa en grupp

När du har skapat några grupper kan du visa dem genom att välja fliken grupper.

![Fliken grupper](media/service-map/machine-groups-tab.png)

Välj sedan grupp namnet för att visa kartan för den dator gruppen.
![Dator grupp ](media/service-map/machine-group.png) de datorer som tillhör gruppen beskrivs i vitt i kartan.

Om du expanderar gruppen visas en lista över de datorer som utgör dator gruppen.

![Dator grupp datorer](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrera efter processer

Du kan växla mellan att visa alla processer och anslutningar i gruppen och bara de som är direkt relaterade till dator gruppen.  Standardvyn är att visa alla processer.  Du kan ändra vyn genom att klicka på filter ikonen ovanför kartan.

![Filtrera grupp](media/service-map/machine-groups-filter.png)

När **alla processer** är markerad kommer kartan att inkludera alla processer och anslutningar på var och en av datorerna i gruppen.

![Dator grupp alla processer](media/service-map/machine-groups-all.png)

Om du ändrar vyn så att endast **grupp anslutna processer** visas begränsas kartan endast till de processer och anslutningar som är direkt anslutna till andra datorer i gruppen, vilket skapar en förenklad vy.

![Filtrerade processer för dator grupper](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Lägga till datorer i en grupp

Om du vill lägga till datorer i en befintlig grupp markerar du kryss rutorna bredvid de datorer som du vill använda och klickar sedan på **Lägg till i grupp**.  Välj sedan den grupp som du vill lägga till datorerna i.
 
### <a name="removing-machines-from-a-group"></a>Ta bort datorer från en grupp

I listan grupper expanderar du grupp namnet för att visa en lista över datorerna i dator gruppen.  Klicka sedan på ellips-menyn bredvid den dator som du vill ta bort och välj **ta bort**.

![Ta bort dator från grupp](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Ta bort eller byta namn på en grupp

Klicka på menyn med tre punkter bredvid grupp namnet i grupp listan.

![Menyn dator grupp](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Roll ikoner

Vissa processer har särskilda roller på datorer: webb servrar, program servrar, databas och så vidare. Tjänstkarta anteckningar i process-och dator rutor med roll ikoner för att snabbt kunna identifiera en process eller servers roll.

| Roll ikon | Description |
|:--|:--|
| ![Webbserver](media/service-map/role-web-server.png) | Webbserver |
| ![Appserver](media/service-map/role-application-server.png) | Program Server |
| ![Databasserver](media/service-map/role-database.png) | Databasserver |
| ![LDAP-server](media/service-map/role-ldap.png) | LDAP-server |
| ![SMB-server](media/service-map/role-smb.png) | SMB-server |

![Roll ikoner](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Misslyckade anslutningar

Misslyckade anslutningar visas i Tjänstkarta Maps för processer och datorer, med en streckad röd linje som visar att ett klient system inte kan komma åt en process eller port. Misslyckade anslutningar rapporteras från alla system med en distribuerad Tjänstkarta-Agent om den här datorn försöker ansluta till den misslyckade anslutningen. Tjänstkarta mäter den här processen genom att observera TCP-socketar som inte upprättar en anslutning. Det här felet kan bero på en brand vägg, en felaktig konfiguration i klienten eller servern eller en fjärrtjänst som inte är tillgänglig.

![Skärm bild av en del av en Tjänstkarta som markerar en streckad röd linje som visar en misslyckad anslutning mellan backup.pl-processen och port 4475.](media/service-map/failed-connections.png)

Att förstå misslyckade anslutningar kan hjälpa till med fel sökning, verifiering av migrering, säkerhets analys och övergripande arkitektur förståelse. Misslyckade anslutningar är ibland ofarliga, men de leder ofta direkt till ett problem, t. ex. en växlings miljö som plötsligt inte går att komma åt, eller två program nivåer som inte kan kommunicera efter en molnbaserad migrering.

## <a name="client-groups"></a>Klient grupper

Klient grupper är rutor på kartan som representerar klient datorer som inte har beroende agenter. En enda klient grupp representerar klienterna för en enskild process eller dator.

![Klient grupper](media/service-map/client-groups.png)

Välj gruppen om du vill se IP-adresserna för servrarna i en klient grupp. Innehållet i gruppen visas i fönstret **Egenskaper för klient grupp** .

![Egenskaper för klient grupp](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Server Port grupper

Server Port grupper är rutor som representerar Server portar på servrar som inte har beroende agenter. Rutan innehåller Server porten och antalet servrar med anslutningar till den porten. Expandera rutan om du vill se enskilda servrar och anslutningar. Om det bara finns en-server i rutan visas namnet eller IP-adressen i listan.

![Server Port grupper](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Snabbmeny

Om du klickar på ellipsen (...) längst upp till höger på en server visas snabb menyn för servern.

![Skärm bild som visar den öppna snabb menyn för en server i Tjänstkarta. Menyn innehåller alternativen läsa in Server karta och Visa själv länkar.](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Läsa in Server karta

Genom att klicka på **Läs in Server karta** går du till en ny karta med den valda servern som den nya fokus datorn.

### <a name="show-self-links"></a>Visa själv länkar

Om du klickar på **Visa själv länkar** ritas noden om, inklusive eventuella själv länkar, som är TCP-anslutningar som börjar och slutar med processer på servern. Om själv länkar visas, ändras meny kommandot för att **dölja själv länkar** så att du kan inaktivera dem.

## <a name="computer-summary"></a>Sammanfattning av dator

Fönstret **dator Sammanfattning** innehåller en översikt över en servers operativ system, antal beroenden och data från andra lösningar. Dessa data omfattar prestanda mått, Service Desk-biljetter, ändrings spårning, säkerhet och uppdateringar.

![Fönstret dator Sammanfattning](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Egenskaper för dator och process

När du navigerar en Tjänstkarta karta kan du välja datorer och processer för att få ytterligare kontext om deras egenskaper. Datorer tillhandahåller information om DNS-namn, IPv4-adresser, processor-och minnes kapacitet, VM-typ, operativ system och version, senaste omstart och ID för deras OMS-och Tjänstkartas agenter.

![Rutan dator egenskaper](media/service-map/machine-properties.png)

Du kan samla in process information från operativ systemets metadata om att köra processer, inklusive process namn, process beskrivning, användar namn och domän (i Windows), företags namn, produkt namn, produkt version, arbets katalog, kommando rad och processens start tid.

![Fönstret process egenskaper](media/service-map/process-properties.png)

I fönstret **process Sammanfattning** får du ytterligare information om processens anslutnings barhet, inklusive dess bundna portar, inkommande och utgående anslutningar och misslyckade anslutningar.

![Process sammanfattnings fönster](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Aviserings integrering

Tjänstkarta integreras med Azure-aviseringar för att Visa utlöst aviseringar för den valda servern i det valda tidsintervallet. Servern visar en ikon om det finns aktuella aviseringar och fönstret med **dator aviseringar** visar aviseringarna.

![Fönstret för dator aviseringar](media/service-map/machine-alerts.png)

Om du vill aktivera Tjänstkarta Visa relevanta aviseringar skapar du en varnings regel som utlöses för en speciell dator. Så här skapar du korrekta aviseringar:
- Inkludera en sats att gruppera efter dator (till exempel **efter dator intervall 1 minut**).
- Välj att avisera utifrån mått mått.

## <a name="log-events-integration"></a>Logg händelse integrering

Tjänstkarta integreras med loggs ökningen för att visa antalet tillgängliga logg händelser för den valda servern under det valda tidsintervallet. Du kan klicka på valfri rad i listan över antal händelser för att hoppa till loggs ökningen och se enskilda logg händelser.

![Fönster för logg händelser i Machine](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Service Desk-integrering

Tjänstkarta integration med Anslutningsprogram för hantering av IT-tjänster (ITSM) sker automatiskt när båda lösningarna är aktiverade och konfigurerade i din Log Analytics-arbetsyta. Integrationen i Tjänstkarta är märkt med "Service Desk". Mer information finns i [Central hantering av ITSM arbets objekt med hjälp av anslutningsprogram för hantering av IT-tjänster (ITSM)](../platform/itsmc-overview.md).

**Skriv bords fönstret för Machine service** visar alla IT Service Management-händelser för den valda servern i det valda tidsintervallet. Servern visar en ikon om det finns aktuella objekt och dator tjänstens Skriv bords fönster visar dem.

![Skriv bords fönstret för Machine service](media/service-map/service-desk.png)

Om du vill öppna objektet i din anslutna ITSM-lösning klickar du på **Visa arbets objekt**.

Om du vill visa information om objektet i loggs ökningen klickar du på **Visa i loggs ökning**.
Anslutnings mått skrivs till två nya tabeller i Log Analytics 

## <a name="change-tracking-integration"></a>Ändringsspårning-integrering

Tjänstkarta integration med Ändringsspårning sker automatiskt när båda lösningarna är aktiverade och konfigurerade i din Log Analytics-arbetsyta.

I rutan **dator ändringsspårning** visas alla ändringar, med den senaste först, tillsammans med en länk för att gå nedåt till loggs ökning för ytterligare information.

![Skärm bild av datorns Ändringsspårnings fönster i Tjänstkarta.](media/service-map/change-tracking.png)

Följande bild är en detaljerad vy över en ConfigurationChange-händelse som du kan se när du har valt **Visa i Log Analytics**.

![ConfigurationChange-händelse](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Prestanda integrering

Fönstret **dator prestanda** visar standard prestanda mått för den valda servern. Måtten innefattar processor användning, minnes användning, nätverks byte som skickats och tagits emot och en lista över de högsta processerna efter skickade och mottagna nätverks byte.

![Fönstret dator prestanda](media/service-map/machine-performance.png)

Om du vill visa prestanda data kan du behöva [Aktivera lämpliga Log Analytics prestanda räknare](../agents/data-sources-performance-counters.md).  De räknare som du vill aktivera:

Windows:
- Processor (*) \\ % processor tid
- Minne \\ % allokerade byte som används
- Nätverkskort (*) \\ skickade byte per sekund
- Nätverkskort (*) \\ mottagna byte/SEK

Linux:
- Processor (*) \\ % processor tid
- Minne (*) \\ % använt minne
- Nätverkskort (*) \\ skickade byte per sekund
- Nätverkskort (*) \\ mottagna byte/SEK

Om du vill hämta data om nätverks prestanda måste du också ha aktiverat Wire Data 2.0-lösningen i din arbets yta.
 
## <a name="security-integration"></a>Säkerhets integrering

Tjänstkarta integration med Säkerhet och granskning sker automatiskt när båda lösningarna är aktiverade och konfigurerade i din Log Analytics-arbetsyta.

I fönstret **dator säkerhet** visas data från säkerhet och granskning-lösningen för den valda servern. Rutan visar en sammanfattning av eventuella utestående säkerhets problem för servern under det valda tidsintervallet. Om du klickar på någon av säkerhets problemen går du vidare till loggs ökningen.

![Fönstret dator säkerhet](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Uppdaterings integrering

Tjänstkarta integration med Uppdateringshantering sker automatiskt när båda lösningarna är aktiverade och konfigurerade i din Log Analytics-arbetsyta.

I fönstret **dator uppdateringar** visas data från uppdateringshantering-lösningen för den valda servern. Rutan visar en sammanfattning av eventuella uppdateringar som saknas för servern under det valda tidsintervallet.

![Skärm bild av fönstret med dator uppdateringar i Tjänstkarta.](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics-poster

Tjänstkarta dator-och process inventerings data är tillgängliga för [sökning](../log-query/log-query-overview.md) i Log Analytics. Du kan använda dessa data i scenarier som omfattar migrerings planering, kapacitets analys, identifiering och prestanda fel sökning på begäran.

En post skapas per timme för varje unik dator och process, förutom de poster som genereras när en process eller dator startar eller aktive ras för att Tjänstkarta. Dessa poster har egenskaperna i följande tabeller. Fälten och värdena i ServiceMapComputer_CL händelser mappar till fält i dator resursen i ServiceMap Azure Resource Manager API. Fälten och värdena i ServiceMapProcess_CL händelser mappar till fälten i process resursen i ServiceMap Azure Resource Manager API. Fältet ResourceName_s matchar fältet namn i motsvarande Resource Manager-resurs. 

>[!NOTE]
>När Tjänstkarta funktioner växer kan dessa fält ändras.

Det finns internt genererade egenskaper som du kan använda för att identifiera unika processer och datorer:

- Dator: Använd *ResourceID* eller *ResourceName_s* för att unikt identifiera en dator i en Log Analytics arbets yta.
- Process: Använd *ResourceID* för att unikt identifiera en process inom en Log Analytics-arbetsyta. *ResourceName_s* är unikt inom kontexten för den dator där processen körs (MachineResourceName_s) 

Eftersom det kan finnas flera poster för en angiven process och dator inom ett angivet tidsintervall, kan frågor returnera fler än en post för samma dator eller process. Om du bara vill ta med den senaste posten lägger du till | deduplicera ResourceId "till frågan.

### <a name="connections"></a>Anslutningar

Anslutnings mått skrivs till en ny tabell i Log Analytics-VMConnection. Den här tabellen innehåller information om anslutningarna för en dator (inkommande och utgående). Anslutnings måtten exponeras också med API: er som gör det möjligt att hämta ett mått i ett tids fönster.  TCP-anslutningar som orsakas av att de godkänns på en lyssnande socket är inkommande, medan de som skapas genom att ansluta till en specifik IP-adress och port är utgående. Riktningen för en anslutning representeras av egenskapen Direction, som kan anges till antingen **inkommande** eller **utgående**. 

Poster i dessa tabeller genereras från data som rapporteras av beroende agenten. Varje post representerar en observation över ett tidsintervall med en minut. Egenskapen TimeGenerated anger start tiden för tidsintervallet. Varje post innehåller information för att identifiera respektive entitet, det vill säga anslutning eller port, samt mått som är associerade med den entiteten. För närvarande rapporteras endast nätverks aktivitet som sker med TCP över IPv4.

För att hantera kostnader och komplexitet representerar anslutnings posterna inte enskilda fysiska nätverks anslutningar. Flera fysiska nätverks anslutningar är grupperade i en logisk anslutning, som sedan återspeglas i respektive tabell.  Det innebär att poster i *VMConnection* -tabellen representerar en logisk gruppering och inte de enskilda fysiska anslutningar som observeras. Den fysiska nätverks anslutningen som delar samma värde för följande attribut under ett angivet minut intervall sammanställs i en enda logisk post i *VMConnection*. 

| Egenskap | Beskrivning |
|:--|:--|
| `Direction` |Anslutningens riktning, värdet är *inkommande* eller *utgående* |
| `Machine` |Datorns FQDN |
| `Process` |Identitet för process eller grupper av processer, initierar/accepterar anslutningen |
| `SourceIp` |Källans IP-adress |
| `DestinationIp` |Målets IP-adress |
| `DestinationPort` |Port nummer för målet |
| `Protocol` |Protokoll som används för anslutningen.  Värdena är *TCP*. |

För att kunna utnyttja gruppens påverkan finns information om antalet grupperade fysiska anslutningar i följande egenskaper för posten:

| Egenskap | Beskrivning |
|:--|:--|
| `LinksEstablished` |Antalet fysiska nätverks anslutningar som har upprättats under rapport tids perioden |
| `LinksTerminated` |Antalet fysiska nätverks anslutningar som har avbrutits under rapport tids perioden |
| `LinksFailed` |Antalet fysiska nätverks anslutningar som har misslyckats under rapporterings tids perioden. Den här informationen är för närvarande endast tillgänglig för utgående anslutningar. |
| `LinksLive` |Antalet fysiska nätverks anslutningar som öppnades i slutet av tids perioden för rapportering|

#### <a name="metrics"></a>Mått

Förutom antalet anslutningar räknas information om mängden data som skickas och tas emot på en specifik logisk anslutning eller nätverks port också med följande egenskaper för posten:

| Egenskap | Beskrivning |
|:--|:--|
| `BytesSent` |Totalt antal byte som har skickats under rapport tids perioden |
| `BytesReceived` |Totalt antal byte som har tagits emot under rapport tids perioden |
| `Responses` |Antalet svar som observerats under rapporterings tids perioden. 
| `ResponseTimeMax` |Den största svars tid (millisekunder) som observeras under rapporterings tids perioden.  Om inget värde anges är egenskapen tom.|
| `ResponseTimeMin` |Den minsta svars tid (millisekunder) som observeras under rapporterings tids perioden.  Om inget värde anges är egenskapen tom.|
| `ResponseTimeSum` |Summan av alla svars tider (millisekunder) som observeras under rapporterings tids perioden.  Om inget värde anges är egenskapen tom|

Den tredje typen av data som rapporteras är svars tid – hur lång tid en anropare ägnar åt att vänta på att en begäran skickas över en anslutning som ska bearbetas och besvaras av Fjärrslutpunkten. Svars tiden som rapporteras är en uppskattning av den sanna svars tiden för det underliggande applikations protokollet. Den beräknas med hjälp av heuristik baserat på observationen av data flödet mellan käll-och mål slutet av en fysisk nätverks anslutning. Konceptuellt är skillnaden mellan den tid då den sista byten av en begäran lämnar avsändaren och den tid då den sista byten av svaret kommer tillbaka till den. Dessa två tidsstämplar används för att avgränsa begär ande-och svars händelser på en specifik fysisk anslutning. Skillnaden mellan dem representerar svars tiden för en enskild begäran. 

I den här första versionen av den här funktionen är vår algoritm en uppskattning som kan fungera med varierande grad av lyckade beroende på det faktiska program protokollet som används för en viss nätverks anslutning. Till exempel fungerar den aktuella metoden bra för Request-Response-baserade protokoll som t. ex. HTTP (S), men fungerar inte med envägs-eller Message Queue-baserade protokoll.

Här är några viktiga saker att tänka på:

1. Om en process accepterar anslutningar på samma IP-adress men över flera nätverks gränssnitt rapporteras en separat post för varje gränssnitt. 
2. Poster med IP-adress med jokertecken kommer inte att innehålla någon aktivitet. De är inkluderade för att representera det faktum att en port på datorn är öppen för inkommande trafik.
3. För att minska utförligheten och data volymen utelämnas poster med IP-adress med jokertecken när det finns en matchande post (för samma process, port och protokoll) med en speciell IP-adress. När en IP-post med jokertecken utelämnas, anges IsWildcardBind Record-egenskapen med den angivna IP-adressen till "true" för att indikera att porten exponeras för varje gränssnitt på rapporterings datorn.
4. Portar som endast är kopplade till ett speciellt gränssnitt har IsWildcardBind inställt på "false".

#### <a name="naming-and-classification"></a>Namn och klassificering

IP-adressen för fjärran sluten av en anslutning ingår i egenskapen RemoteIp för att under lätta. För inkommande anslutningar är RemoteIp detsamma som SourceIp, medan för utgående anslutningar är det samma som DestinationIp. Egenskapen RemoteDnsCanonicalNames representerar DNS-kanoniska namn som rapporteras av datorn för RemoteIp. Egenskaperna RemoteDnsQuestions och RemoteClassification är reserverade för framtida användning. 

#### <a name="geolocation"></a>Geolocation

*VMConnection* innehåller också information om geolokalisering för fjärrparten av varje anslutnings post i följande egenskaper för posten: 

| Egenskap | Beskrivning |
|:--|:--|
| `RemoteCountry` |Namnet på det land/den region som är värd för RemoteIp.  Till exempel *USA* |
| `RemoteLatitude` |Den geolokalisering på latitud.  Till exempel *47,68* |
| `RemoteLongitude` |Longituden för den här platsen.  Till exempel *– 122,12* |

#### <a name="malicious-ip"></a>Skadlig IP

Varje RemoteIp-egenskap i *VMConnection* -tabellen kontrol leras mot en uppsättning IP-adresser med känd skadlig aktivitet. Om RemoteIp identifieras som skadlig kommer följande egenskaper att fyllas i (de är tomma, om IP-adressen inte betraktas som skadlig) i följande egenskaper för posten:

| Egenskap | Beskrivning |
|:--|:--|
| `MaliciousIp` |RemoteIp-adressen |
| `IndicatorThreadType` |En hot indikator upptäcktes av följande värden: *botnät*, *C2*, *CryptoMining*, *Darknet*, *DDoS*, *MaliciousUrl*, *malware*, *phishing*, *proxy*, *oönskade program*, *visnings lista*.   |
| `Description` |Beskrivning av det observerade hotet. |
| `TLPLevel` |TLP-nivån (trafik ljus protokoll) är en av de definierade värdena, *vitt*, *grönt*, *gult*, *rött*. |
| `Confidence` |Värdena är *0 – 100*. |
| `Severity` |Värdena är *0 – 5*, där *5* är det allvarligaste och *0* inte är allvarligt. Standardvärdet är *3*.  |
| `FirstReportedDateTime` |Första gången som providern rapporterade indikatorn. |
| `LastReportedDateTime` |Den senaste gången indikatorn visades vid ett flöde. |
| `IsActive` |Anger att indikatorer inaktive ras med värdet *True* eller *false* . |
| `ReportReferenceLink` |Länkar till rapporter som är relaterade till en bestämd som kan observeras. |
| `AdditionalInformation` |Innehåller ytterligare information om det observerade hotet. |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL poster

Poster med en typ av *ServiceMapComputer_CL* har inventerings data för servrar med Tjänstkartas agenter. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Den unika identifieraren för en dator på arbets ytan |
| `ResourceName_s` | Den unika identifieraren för en dator på arbets ytan |
| `ComputerName_s` | Datorns FQDN |
| `Ipv4Addresses_s` | En lista över serverns IPv4-adresser |
| `Ipv6Addresses_s` | En lista över serverns IPv6-adresser |
| `DnsNames_s` | En matris med DNS-namn |
| `OperatingSystemFamily_s` | Windows eller Linux |
| `OperatingSystemFullName_s` | Det fullständiga namnet på operativ systemet  |
| `Bitness_s` | Bitness för datorn (32-bitars eller 64-bitars)  |
| `PhysicalMemory_d` | Fysiskt minne i MB |
| `Cpus_d` | Antalet processorer |
| `CpuSpeed_d` | CPU-hastigheten i MHz|
| `VirtualizationState_s` | *okänd*, *fysisk*, *virtuell*, *hypervisor* |
| `VirtualMachineType_s` | *HyperV*, *VMware* och så vidare |
| `VirtualMachineNativeMachineId_g` | Det VM-ID som tilldelats av dess hypervisor |
| `VirtualMachineName_s` | Namnet på den virtuella datorn |
| `BootTime_t` | Start tiden |

### <a name="servicemapprocess_cl-type-records"></a>ServiceMapProcess_CL Skriv poster

Poster med en typ av *ServiceMapProcess_CL* har inventerings data för TCP-anslutna processer på servrar med Tjänstkartas agenter. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Den unika identifieraren för en process inom arbets ytan |
| `ResourceName_s` | Den unika identifieraren för en process på den dator där den körs|
| `MachineResourceName_s` | Datorns resurs namn |
| `ExecutableName_s` | Namnet på den körbara processen |
| `StartTime_t` | Start tid för process bassäng |
| `FirstPid_d` | Det första PID i lagringspoolen |
| `Description_s` | Beskrivning av processen |
| `CompanyName_s` | Företagets namn |
| `InternalName_s` | Det interna namnet |
| `ProductName_s` | Produktens namn |
| `ProductVersion_s` | Produkt versionen |
| `FileVersion_s` | Fil versionen |
| `CommandLine_s` | Kommando raden |
| `ExecutablePath _s` | Sökvägen till den körbara filen |
| `WorkingDirectory_s` | Arbets katalogen |
| `UserName` | Kontot under vilket processen körs |
| `UserDomain` | Domänen som processen körs under |

## <a name="sample-log-searches"></a>Exempel på loggsökningar

### <a name="list-all-known-machines"></a>Visa en lista med alla kända datorer

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Ange kapaciteten för fysiskt minne för alla hanterade datorer.

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`

### <a name="list-computer-name-dns-ip-and-os"></a>Visa dator namn, DNS, IP och OS.

`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Hitta alla processer med "SQL" på kommando raden

`ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Hitta en dator (senaste posten) efter resurs namn

`search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Hitta en dator (senaste posten) via IP-adress

`search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-known-processes-on-a-specified-machine"></a>Visa en lista med alla kända processer på en angiven dator

`ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-computers-running-sql"></a>Lista alla datorer som kör SQL

`ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Visa en lista med alla unika produkt versioner av sväng i mitt Data Center

`ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Skapa en dator grupp för alla datorer som kör CentOS

`ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Sammanfatta utgående anslutningar från en grupp datorer

```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>REST-API

Alla Server-, process-och beroende data i Tjänstkarta är tillgängliga via [Tjänstkarta REST API](/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Diagnostik-och användnings data

Microsoft samlar automatiskt in användnings-och prestanda data via din användning av tjänsten Tjänstkarta. Microsoft använder dessa data för att tillhandahålla och förbättra kvaliteten, säkerheten och integriteten i Tjänstkartas tjänsten. För att tillhandahålla korrekta och effektiva fel söknings funktioner innehåller data information om konfigurationen av program varan, till exempel operativ system och version, IP-adress, DNS-namn och arbets Stations namn. Microsoft samlar inte in namn, adresser eller annan kontakt information.

Mer information om insamling och användning av data finns i [sekretess policy för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="next-steps"></a>Nästa steg

Läs mer om [loggs ökningar](../log-query/log-query-overview.md) i Log Analytics för att hämta data som samlas in av tjänstkarta.

## <a name="troubleshooting"></a>Felsökning

Om du har problem med att installera eller köra Tjänstkarta kan det här avsnittet hjälpa dig. Om du fortfarande inte kan lösa problemet kontaktar du Microsoft Support.

### <a name="dependency-agent-installation-problems"></a>Installations problem för beroende agent

#### <a name="installer-prompts-for-a-reboot"></a>Installationsprogrammet begär en omstart
Beroende agenten kräver *vanligt vis* ingen omstart vid installation eller borttagning. I vissa sällsynta fall kräver dock Windows Server en omstart för att fortsätta med en installation. Detta inträffar när ett beroende, vanligt vis kräver Microsoft Visual C++ Redistributable library en omstart på grund av en låst fil.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>Meddelandet ”Det går inte att installera Dependency Agent: Det gick inte att installera Visual Studio Runtime-bibliotek (code = [code_number])” visas

Microsoft Dependency Agent bygger på Microsoft Visual Studio-körningsbiblioteken. Du får ett meddelande om det uppstår problem under installationen av biblioteken. 

Installationsprogrammen för körningsbiblioteken skapar loggar i mappen %LOCALAPPDATA%\temp. Filen är `dd_vcredist_arch_yyyymmddhhmmss.log` , där " *båge* " `x86` är eller `amd64` och *yyyymmddhhmmss* är datum och tid (24-timmarsformat) när loggen skapades. Loggen innehåller information om det problem som blockerar installationen.

Det kan vara bra att installera [de senaste körningsbiblioteken](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) först.

I följande tabell visas kod nummer och föreslagna lösningar.

| Kod | Beskrivning | Lösning |
|:--|:--|:--|
| 0x17 | Biblioteksinstallationsprogrammet kräver en Windows-uppdatering som inte har installerats. | Titta i den senaste biblioteksinstallationsloggen.<br><br>Om en referens till `Windows8.1-KB2999226-x64.msu` följs av en rad uppfyller `Error 0x80240017: Failed to execute MSU package,` du inte kraven för att installera KB2999226. Följ anvisningarna i avsnittet med förhandskrav i artikeln om [Universal C Runtime i Windows](https://support.microsoft.com/kb/2999226). Du kan behöva köra Windows Update och starta om flera gånger för att installera det som krävs för att uppfylla förhandskraven.<br><br>Kör installationsprogrammet för Microsoft Dependency Agent igen. |

### <a name="post-installation-issues"></a>Problem efter installationen

#### <a name="server-doesnt-appear-in-service-map"></a>Servern visas inte i Tjänstkarta

Om beroende Agent installationen lyckades, men du inte ser datorn i Tjänstkarta-lösningen:
* Har Dependency Agent installerats på rätt sätt? Du kan validera detta genom att kontrollera om tjänsten är installerad och körs.<br><br>
**Windows**: Sök efter tjänsten med namnet **Microsoft Dependency agent**.
**Linux**: leta efter den process som körs av **Microsoft-beroende-agenten**.

* Är du på den [Log Analytics kostnads fria nivån](https://azure.microsoft.com/pricing/details/monitor/)? Den kostnadsfria planen tillåter upp till fem unika datorer med Tjänstkarta. Efterföljande datorer visas inte i Tjänstkarta, även om de fem föregående inte längre skickar data.

* Skickar servern logg-och perf-data till Azure Monitor loggar? Gå till Monitor\Logs och kör följande fråga för datorn: 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

Fick du flera olika händelser i resultatet? Är data aktuella? I så fall fungerar Log Analytics-agenten som den ska och kommunicerar med arbetsytan. Om inte kontrollerar du agenten på datorn: [Log Analytics-agent för Windows-felsökning](../agents/agent-windows-troubleshoot.md) eller [Log Analytics-agent för Linux-felsökning](../agents/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Servern visas i Tjänstkarta men saknar processer

Om du ser datorn i Tjänstkarta, men inte har någon process-eller anslutnings data, som anger att beroende agenten är installerad och körs, men kernel-drivrutinen inte lästes in. 

Kontrol lera `C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file` (Windows) eller `/var/opt/microsoft/dependency-agent/log/service.log file` (Linux). De sista raderna i filen anger varför kerneln inte har lästs in. Till exempel kanske din kernel inte stöds i Linux om du har uppdaterat den.

## <a name="suggestions"></a>Förslag

Har du kommentarer till oss om Tjänstkarta eller den här dokumentationen?  Besök vår [användares röst sida](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)där du kan föreslå funktioner eller rösta på befintliga förslag.