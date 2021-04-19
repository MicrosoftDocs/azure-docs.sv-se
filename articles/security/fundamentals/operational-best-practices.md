---
title: Metodtips för säkerhet för dina Azure-tillgångar
titleSuffix: Azure security
description: Den här artikeln innehåller en uppsättning metodtips för att skydda dina data, program och andra tillgångar i Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 6634a536828b3c19d771d135fdb3a1224d3dfdf3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717431"
---
# <a name="azure-operational-security-best-practices"></a>Metodtips för driftsäkerhet i Azure
Den här artikeln innehåller en uppsättning metodtips för att skydda dina data, program och andra tillgångar i Azure.

Metodtipsen baseras på en konsensus och de arbetar med aktuella funktioner och funktionsuppsättningar för Azure-plattformen. Åsikter och tekniker ändras med tiden och den här artikeln uppdateras regelbundet för att återspegla dessa ändringar.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definiera och distribuera starka metoder för driftsäkerhet
Driftsäkerhet i Azure avser de tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra tillgångar i Azure. Den operativa säkerheten i Azure bygger på ett ramverk som införlivar de kunskaper som gjorts via funktioner som är unika för Microsoft, inklusive [SDL (Security Development Lifecycle),](https://www.microsoft.com/sdl) [Microsoft Security Response Center-programmet](https://www.microsoft.com/msrc?rtc=1) och djup medvetenhet om hotlandskapet inom cybersäkerhet.

## <a name="manage-and-monitor-user-passwords"></a>Hantera och övervaka användarlösenord
I följande tabell visas några metodtips för hantering av användarlösenord:

**Bästa praxis:** Se till att du har rätt nivå av lösenordsskydd i molnet.   
**Information:** Följ riktlinjerna i [Microsofts](https://www.microsoft.com/research/publication/password-guidance/)lösenordsvägledning, som är begränsad till användare av Microsofts identitetsplattformar (Azure Active Directory, Active Directory och Microsoft-konto).

**Bästa praxis:** Övervaka misstänkta åtgärder relaterade till dina användarkonton.   
**Information:** Övervaka för [användare i riskzonen](../../active-directory/identity-protection/overview-identity-protection.md) [och riskfyllda inloggningar med hjälp](../../active-directory/identity-protection/overview-identity-protection.md) av Azure AD-säkerhetsrapporter.

**Bästa praxis:** Identifiera och åtgärda lösenord med hög risk automatiskt.   
**Information:** [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) är en funktion i Azure AD Premium P2-versionen som gör att du kan:

- Identifiera potentiella säkerhetsrisker som påverkar organisationens identiteter
- Konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till organisationens identiteter
- Undersöka misstänkta incidenter och vidta lämpliga åtgärder för att lösa dem

## <a name="receive-incident-notifications-from-microsoft"></a>Ta emot incidentmeddelanden från Microsoft
Se till att ditt säkerhetsdriftsteam får meddelanden om Azure-incidenter från Microsoft. Ett incidentmeddelande gör att ditt säkerhetsteam vet att du har komprometterat Azure-resurser så att de snabbt kan reagera på och åtgärda potentiella säkerhetsrisker.

I Azure-registreringsportalen kan du se till att administratörskontaktinformationen innehåller information som meddelar säkerhetsåtgärder. Kontaktinformation är en e-postadress och ett telefonnummer.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organisera Azure-prenumerationer i hanteringsgrupper
Om din organisation har många prenumerationer kanske du behöver ett sätt att effektivt hantera åtkomst, principer och efterlevnad för dessa prenumerationer. [Azure-hanteringsgrupper](../../governance/management-groups/create-management-group-portal.md) tillhandahåller en omfångsnivå som ligger ovanför prenumerationerna. Du ordnar dina prenumerationer i behållare som kallas hanteringsgrupper och tillämpar styrningsvillkor på de olika hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de villkor som tillämpas för hanteringsgruppen.

Du kan skapa en flexibel struktur för hanteringsgrupper och prenumerationer i en katalog. Varje katalog får en hanteringsgrupp på den översta nivån som kallas rothanteringsgruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Rothanteringsgruppen tillåter att globala principer och Azure-rolltilldelningar tillämpas på katalognivå.

Här är några metodtips för att använda hanteringsgrupper:

**Bästa praxis:** Se till att nya prenumerationer tillämpar styrningselement som principer och behörigheter när de läggs till.   
**Information:** Använd rothanteringsgruppen för att tilldela säkerhetselement för hela företaget som gäller för alla Azure-tillgångar. Principer och behörigheter är exempel på element.

**Bästa praxis:** Justera de översta nivåerna av hanteringsgrupper med segmenteringsstrategi för att ge en punkt för kontroll och principkonsekvens i varje segment.   
**Information:** Skapa en enskild hanteringsgrupp för varje segment under rothanteringsgruppen. Skapa inte några andra hanteringsgrupper under roten.

**Bästa praxis:** Begränsa hanteringsgruppens djup för att undvika förvirring som stör både åtgärder och säkerhet.   
**Information:** Begränsa hierarkin till tre nivåer, inklusive roten.

**Bästa praxis:** Välj noggrant vilka objekt som ska tillämpas på hela företaget med rothanteringsgruppen.   
**Information:** Se till att element i rothanteringsgruppen har ett tydligt behov av att tillämpas för varje resurs och att de har låg påverkan.

Bra kandidater är:

- Regelkrav som har en tydlig affärspåverkan (till exempel begränsningar som rör datasuveränitet)
- Krav med nästan noll potentiella negativa effekter på åtgärder, till annat som en princip med granskningseffekt eller Azure RBAC-behörighetstilldelningar som har granskats noggrant

**Bästa praxis:** Planera och testa noggrant alla ändringar i rothanteringsgruppen i hela företaget innan du tillämpar dem (princip, Azure RBAC-modell och så vidare).   
**Information:** Ändringar i rothanteringsgruppen kan påverka alla resurser i Azure. De är ett kraftfullt sätt att säkerställa konsekvens i hela företaget, men fel eller felaktig användning kan påverka produktionsåtgärder negativt. Testa alla ändringar i rothanteringsgruppen i ett testlabb eller ett produktionstest.

## <a name="streamline-environment-creation-with-blueprints"></a>Effektivisera skapandet av miljön med skisser
[Tjänsten Azure Blueprints](../../governance/blueprints/overview.md) molnarkitekter och centrala it-grupper kan definiera en upprepningsbar uppsättning Azure-resurser som implementerar och följer en organisations standarder, mönster och krav. Azure Blueprints gör det möjligt för utvecklingsteam att snabbt skapa och skapa nya miljöer med en uppsättning inbyggda komponenter och förtroende för att de skapar dessa miljöer inom organisationens efterlevnad.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Övervaka lagringstjänster för oväntade ändringar i beteendet
Det kan vara mer komplicerat att diagnostisera och felsöka problem i ett distribuerat program i en molnmiljö än i traditionella miljöer. Program kan distribueras i en PaaS- eller IaaS-infrastruktur, lokalt, på en mobil enhet eller i någon kombination av dessa miljöer. Programmets nätverkstrafik kan passera offentliga och privata nätverk och programmet kan använda flera lagringstekniker.

Du bör kontinuerligt övervaka lagringstjänsterna som programmet använder för oväntade ändringar i beteendet (till exempel långsammare svarstider). Använd loggning för att samla in mer detaljerade data och analysera ett problem på djupet. Diagnostikinformationen som du får från både övervakning och loggning hjälper dig att fastställa rotorsaken till problemet som programmet påträffade. Sedan kan du felsöka problemet och fastställa lämpliga steg för att åtgärda det.

[Azure-lagringsanalys](../../storage/common/storage-analytics.md) utför loggning och tillhandahåller måttdata för ett Azure Storage-konto. Vi rekommenderar att du använder dessa data för att spåra begäranden, analysera användningstrender och diagnostisera problem med ditt lagringskonto.

## <a name="prevent-detect-and-respond-to-threats"></a>Förhindra, identifiera och svara på hot
[Azure Security Center](../../security-center/security-center-introduction.md) hjälper dig att förhindra, identifiera och svara på hot genom att ge bättre insyn i (och kontroll över) säkerheten för dina Azure-resurser. Det ger integrerad säkerhetsövervakning och principhantering i alla dina Azure-prenumerationer, hjälper till att identifiera hot som annars skulle gå ouppmärkt förbi och fungerar med olika säkerhetslösningar.

Den kostnadsfria nivån av Security Center endast begränsad säkerhet för dina Azure-resurser. På standardnivån utökas dessa funktioner till lokala och andra moln. Security Center Standard hjälper dig att hitta och åtgärda säkerhetsproblem, tillämpa åtkomst- och programkontroller för att blockera skadlig aktivitet, identifiera hot med hjälp av analys och intelligens och svara snabbt vid angrepp. Du kan prova Security Center Standard utan kostnad under de första 60 dagarna. Vi rekommenderar att du [uppgraderar din Azure-prenumeration till Security Center Standard](../../security-center/security-center-get-started.md).

Använd Security Center för att få en central vy över säkerhetstillståndet för alla dina Azure-resurser. Kontrollera snabbt att lämpliga säkerhetskontroller är på plats och konfigurerade korrekt, och identifiera snabbt alla resurser som behöver åtgärdas.

Security Center integreras också med [Microsoft Defender Avancerat skydd (ATP)](../../security-center/security-center-wdatp.md), som tillhandahåller omfattande funktioner för slutpunktsidentifiering och svar (EDR). Med Microsoft Defender ATP-integrering kan du upptäcka avvikelser. Du kan också identifiera och svara på avancerade attacker på serverslutpunkter som övervakas av Security Center.

Nästan alla företagsorganisationer har ett SIEM-system (Säkerhetsinformations- och händelsehantering) som hjälper dig att identifiera nya hot genom att konsolidera logginformation från olika signalinsamlingsenheter. Loggarna analyseras sedan av ett dataanalyssystem för att identifiera vad som är "intressant" utifrån det brus som är oundvikligt i alla logginsamlings- och analyslösningar.

[Azure Sentinel](../../sentinel/overview.md) är en skalbar, molnbaserad lösning för säkerhetsinformation och händelsehantering (SIEM) och soar-lösning (security orchestration automated response). Azure Sentinel tillhandahåller intelligent säkerhetsanalys och hotinformation via aviseringsidentifiering, hotsynlighet, proaktiv jakt och automatiserad hotsvar.

Här är några metodtips för att förhindra, identifiera och svara på hot:

**Bästa praxis:** Öka hastigheten och skalbarheten för din SIEM-lösning med hjälp av en molnbaserad SIEM.   
**Information:** Undersök funktionerna i [Azure Sentinel](../../sentinel/overview.md) och jämför dem med funktionerna i det du för närvarande använder lokalt. Överväg att Azure Sentinel om det uppfyller organisationens SIEM-krav.

**Bästa praxis:** Hitta de allvarligaste säkerhetsriskerna så att du kan prioritera undersökningen.   
**Information:** Granska dina [Azure-säkerhetspoäng för](../../security-center/secure-score-security-controls.md) att se rekommendationerna från De Inbyggda Azure-principerna och azure-initiativen Azure Security Center. De här rekommendationerna hjälper dig att hantera de största riskerna, till exempel säkerhetsuppdateringar, slutpunktsskydd, kryptering, säkerhetskonfigurationer, waf som saknas, internetanslutna virtuella datorer med mera.

Med säkerhetspoängen, som baseras på CIS-kontroller (Center for Internet Security), kan du jämföra din organisations Azure-säkerhet mot externa källor. Extern verifiering hjälper till att validera och utöka teamets säkerhetsstrategi.

**Bästa praxis:** Övervaka säkerhetsstatusen för datorer, nätverk, lagrings- och datatjänster samt program för att identifiera och prioritera potentiella säkerhetsproblem.  
**Information:** Följ [säkerhetsrekommendationerna](../../security-center/security-center-recommendations.md) i Security Center börjar med objekt med högst prioritet.

**Bästa praxis:** Integrera Security Center-aviseringar i din SIEM-lösning (säkerhetsinformation och händelsehantering).   
**Information:** De flesta organisationer med SIEM använder den som ett centralt informationsställe för säkerhetsaviseringar som kräver ett analytikersvar. Bearbetade händelser som produceras Security Center publiceras i Azure-aktivitetsloggen, en av loggarna som är tillgängliga via Azure Monitor. Azure Monitor en konsoliderad pipeline för att dirigera alla dina övervakningsdata till ett SIEM-verktyg. Anvisningar [finns i Strömma aviseringar till en SIEM-, SOAR- eller IT Service Management-lösning.](../../security-center/export-to-siem.md) Om du använder en Azure Sentinel, se [Anslut Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Bästa praxis:** Integrera Azure-loggar med din SIEM.   
**Information:** Använd [Azure Monitor för att samla in och exportera data](../../azure-monitor/overview.md#integrate-and-export-data). Den här praxis är viktig för att möjliggöra undersökning av säkerhetsincidenter och kvarhållning av onlineloggar är begränsad. Om du använder en Azure Sentinel, se [Ansluta datakällor](../../sentinel/connect-data-sources.md).

**Bästa praxis:** Påskynda dina undersöknings- och jaktprocesser och minska falska positiva identifieringar genom att integrera EDR-funktioner (Slutpunktsidentifiering och svar) i din attackundersökning.   
**Information:** [Aktivera integrering av Microsoft Defender for Endpoint](../../security-center/security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration) via din Security Center säkerhetsprincip. Överväg att Azure Sentinel för hotjakt och incidentsvar.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Övervaka scenariobaserad nätverksövervakning från end-to-end
Kunder skapar ett nätverk från start till slut i Azure genom att kombinera nätverksresurser som ett virtuellt nätverk, ExpressRoute, Application Gateway och lastbalanserare. Övervakning är tillgängligt på var och en av nätverksresurserna.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) är en regional tjänst. Använd dess diagnostik- och visualiseringsverktyg för att övervaka och diagnostisera villkor på nätverksnivå i, till och från Azure.

Följande är metodtips för nätverksövervakning och tillgängliga verktyg.

**Bästa praxis:** Automatisera fjärrnätverksövervakning med paketfångst.  
**Information:** Övervaka och diagnostisera nätverksproblem utan att logga in på dina virtuella datorer med hjälp av Network Watcher. Utlöst [paketfångst](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) genom att ställa in aviseringar och få åtkomst till prestandainformation i realtid på paketnivå. När du ser ett problem kan du undersöka i detalj för att få bättre diagnoser.

**Bästa praxis:** Få insyn i nätverkstrafiken med hjälp av flödesloggar.  
**Information:** Skapa en djupare förståelse av nätverkstrafikens mönster med hjälp av [flödesloggar för nätverkssäkerhetsgrupp.](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) Informationen i flödesloggarna hjälper dig att samla in data för efterlevnad, granskning och övervakning av din nätverkssäkerhetsprofil.

**Bästa praxis:** Diagnostisera problem med VPN-anslutning.  
**Information:** Använd Network Watcher för [att diagnostisera de vanligaste VPN Gateway och anslutningsproblemen](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Du kan inte bara identifiera problemet utan även använda detaljerade loggar för att undersöka vidare.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Säker distribution med hjälp av beprövade DevOps-verktyg
Använd följande DevOps-metodtips för att säkerställa att ditt företag och dina team är produktiva och effektiva.

**Bästa praxis:** Automatisera utvecklingen och distributionen av tjänster.  
**Information:** [Infrastruktur som kod](/azure/devops/learn/what-is-infrastructure-as-code) är en uppsättning tekniker och metoder som hjälper IT-proffs att ta bort belastningen på den dagliga bygget och hanteringen av modulär infrastruktur. Det gör det möjligt för IT-proffs att skapa och underhålla sin moderna servermiljö på ett sätt som liknar hur programutvecklare skapar och underhåller programkod.

Du kan använda [Azure Resource Manager för](../../azure-resource-manager/templates/template-syntax.md) att etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall för att upprepade gånger distribuera ditt program i varje fas av programmets livscykel.

**Bästa praxis:** Skapa och distribuera automatiskt till Azure-webbappar eller molntjänster.  
**Information:** Du kan konfigurera dina Azure DevOps Projects att  [automatiskt skapa och distribuera till](/azure/devops/pipelines/index) Azure-webbappar eller molntjänster. Azure DevOps distribuerar automatiskt binärfilerna efter att ha gjort en version till Azure efter varje kodkontroll. Paketbyggprocessen motsvarar kommandot Package i Visual Studio och publiceringsstegen motsvarar kommandot Publish i Visual Studio.

**Bästa praxis:** Automatisera versionshantering.  
**Information:** [Azure Pipelines](/azure/devops/pipelines/index) är en lösning för att automatisera distribution i flera steg och hantera lanseringsprocessen. Skapa hanterade pipelines för kontinuerlig distribution för att släppa snabbt, enkelt och ofta. Med Azure Pipelines kan du automatisera lanseringsprocessen och du kan ha fördefinierade arbetsflöden för godkännande. Distribuera lokalt och till molnet, utöka och anpassa efter behov.

**Bästa praxis:** Kontrollera appens prestanda innan du startar den eller distribuerar uppdateringar till produktion.  
**Information:** Kör molnbaserade [belastningstester för](/azure/devops/test/load-test/overview#alternatives) att:

- Hitta prestandaproblem i din app.
- Förbättra distributionskvaliteten.
- Kontrollera att din app alltid är tillgänglig.
- Se till att din app kan hantera trafik för nästa lanserings- eller marknadsföringskampanj.

[Apache JMeter](https://jmeter.apache.org/) är ett kostnadsfritt, populärt verktyg med öppen källkod med ett starkt community-stöd.

**Bästa praxis:** Övervaka programprestanda.  
**Information:** [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) är en utökningsbar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Använd Application Insights för att övervaka ditt live-webbprogram. Den identifierar automatiskt prestandaavvikelser. Den innehåller analysverktyg som hjälper dig att diagnostisera problem och förstå vad användarna faktiskt gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

## <a name="mitigate-and-protect-against-ddos"></a>Minimera och skydda mot DDoS
Distribuerad denial of service (DDoS) är en typ av attack som försöker få slut på programresurser. Målet är att påverka programmets tillgänglighet och dess möjlighet att hantera legitima begäranden. Dessa attacker blir mer sofistikerade och större vad gäller storlek och påverkan. De kan riktas mot alla slutpunkter som kan nås offentligt via Internet.

Design och utveckling för DDoS-återhämtning kräver planering och utformning för en mängd olika fellägen. Nedan följer metodtips för att skapa DDoS-motståndskraftiga tjänster i Azure.

**Bästa praxis:** Se till att säkerheten är en prioritet under hela livscykeln för ett program, från design och implementering till distribution och drift. Program kan ha buggar som tillåter en relativt låg volym av begäranden att använda stora resurser, vilket resulterar i ett tjänstavbrott.  
**Information:** För att skydda en tjänst som körs på Microsoft Azure bör du ha en god förståelse för din programarkitektur och fokusera på de fem grundpelarna [för programkvalitet.](/azure/architecture/guide/pillars) Du bör känna till vanliga trafikvolymer, anslutningsmodellen mellan programmet och andra program och de tjänstslutpunkter som exponeras mot det offentliga Internet.

Det är viktigast att se till att ett program är tillräckligt motståndskraftigt för att hantera en dos av tjänsten som är riktad mot själva programmet. Säkerhet och sekretess är inbyggda i Azure-plattformen, från och med [SDL (Security Development Lifecycle).](https://www.microsoft.com/sdl) SDL hanterar säkerheten i varje utvecklingsfasen och säkerställer att Azure uppdateras kontinuerligt för att göra det ännu säkrare.

**Bästa praxis:** Utforma [](/azure/architecture/guide/design-principles/scale-out) dina program så att de skalas horisontellt för att möta behovet av en förstärkt belastning, särskilt i händelse av en DDoS-attack. Om ditt program är beroende av en enda instans av en tjänst skapas en felpunkt. Etablering av flera instanser gör systemet mer motståndskraftigt och skalbart.  
**Information:** [För Azure App Service](../../app-service/overview.md)väljer du [en App Service plan](../../app-service/overview-hosting-plans.md) som erbjuder flera instanser.

Konfigurera Azure Cloud Services var och en av dina roller så att de använder [flera instanser.](../../cloud-services/cloud-services-choose-me.md)

För [Azure Virtual Machines](../../virtual-machines/windows/overview.md)ser du till att arkitekturen för din virtuella dator innehåller fler än en virtuell dator och att varje virtuell dator ingår i en [tillgänglighetsuppsättning.](../../virtual-machines/windows/tutorial-availability-sets.md) Vi rekommenderar att du använder VM-skalningsuppsättningar för funktioner för automatisk skalning.

**Bästa praxis:** Att skikta säkerhetsskydd i ett program minskar risken för en lyckad attack. Implementera säker design för dina program med hjälp av de inbyggda funktionerna i Azure-plattformen.  
**Information:** Risken för angrepp ökar med programmets storlek (yta). Du kan minska ytan genom att använda en godkännandelista för att stänga det exponerade IP-adressutrymmet och lyssnande portar som inte behövs på[lastbalanserarna](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) ( Azure Load Balancer [och Azure Application Gateway](../../application-gateway/application-gateway-create-probe-portal.md)).

[Nätverkssäkerhetsgrupper](../../virtual-network/network-security-groups-overview.md) är ett annat sätt att minska angreppsytan. Du kan använda [](../../virtual-network/network-security-groups-overview.md#application-security-groups) [tjänsttaggar](../../virtual-network/network-security-groups-overview.md#service-tags) och programsäkerhetsgrupper för att minimera komplexiteten för att skapa säkerhetsregler och konfigurera nätverkssäkerhet, som en naturlig förlängning av ett programs struktur.

Du bör distribuera Azure-tjänster i ett [virtuellt nätverk när](../../virtual-network/virtual-networks-overview.md) det är möjligt. På så sätt kan tjänstresurser kommunicera via privata IP-adresser. Azure-tjänsttrafik från ett virtuellt nätverk använder som standard offentliga IP-adresser som käll-IP-adresser.

Med [hjälp av tjänstslutpunkter](../../virtual-network/virtual-network-service-endpoints-overview.md) växlar tjänsttrafik till att använda privata adresser för virtuella nätverk som käll-IP-adresser när de använder Azure-tjänsten från ett virtuellt nätverk.

Vi ser ofta att kundernas lokala resurser attackeras tillsammans med deras resurser i Azure. Om du ansluter en lokal miljö till Azure minimerar du exponeringen av lokala resurser för det offentliga Internet.

Azure har två [DDoS-tjänsterbjudanden som](../../ddos-protection/ddos-protection-overview.md) ger skydd mot nätverksattacker:

- Grundläggande skydd är integrerat i Azure som standard utan extra kostnad. Skalning och kapacitet i det globalt distribuerade Azure-nätverket ger skydd mot vanliga attacker på nätverknivå via ständigt på trafikövervakning och riskreducering i realtid. Basic kräver ingen användarkonfiguration eller programändringar och hjälper till att skydda alla Azure-tjänster, inklusive PaaS-tjänster som Azure DNS.
- Standardskydd ger avancerade DDoS-skyddsfunktioner mot nätverksattacker. Den finjusteras automatiskt för att skydda dina specifika Azure-resurser. Skydd är enkelt att aktivera när du skapar virtuella nätverk. Det kan också göras efter skapandet och kräver inga program- eller resursändringar.

## <a name="enable-azure-policy"></a>Aktivera Azure Policy
[Azure Policy](../../governance/policy/overview.md) är en tjänst i Azure som du använder för att skapa, tilldela och hantera principer. Dessa principer tillämpar regler och effekter på dina resurser, så att resurserna följer företagets standarder och serviceavtal. Azure Policy gör detta genom att utvärdera resursernas kompatibilitet med hjälp av tilldelade principer.

Aktivera Azure Policy att övervaka och framtvinga organisationens skrivna princip. Detta säkerställer efterlevnad av företagets eller regelmässiga säkerhetskrav genom att centralt hantera säkerhetsprinciper i dina hybridmolnarbetsbelastningar. Lär dig hur du [skapar och hanterar principer för att genomdriva efterlevnad.](../../governance/policy/tutorials/create-and-manage.md) Se [Azure Policy för en](../../governance/policy/concepts/definition-structure.md) översikt över elementen i en princip.

Här följer några metodtips för säkerhet när du har Azure Policy:

**Bästa praxis:** Principen stöder flera typer av effekter. Du kan läsa om dem Azure Policy [i definitionsstrukturen](../../governance/policy/concepts/definition-structure.md#policy-rule). Affärsåtgärder kan påverkas negativt av **neka-effekten** och den reparerande  effekten, så börja med granskningseffekten för att begränsa risken för negativ påverkan från principen.    
**Information:** [Starta principdistributioner i granskningsläge](../../governance/policy/concepts/definition-structure.md#policy-rule) och gå sedan vidare **till att neka** eller **åtgärda**. Testa och granska resultatet av granskningseffekten innan du går över **till att neka** eller **åtgärda**.

Mer information finns i Skapa [och hantera principer för att framtvinga efterlevnad.](../../governance/policy/tutorials/create-and-manage.md)

**Bästa praxis:** Identifiera de roller som ansvarar för övervakning av principöverträdelser och se till att rätt reparationsåtgärder vidtas snabbt.   
**Information:** Ha den tilldelade rollövervakarefterlevnaden [via Azure Portal](../../governance/policy/how-to/get-compliance-data.md#portal) eller via [kommandoraden](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Bästa praxis:** Azure Policy är en teknisk representation av en organisations skrivna principer. Mappa alla Azure Policy till organisationsprinciper för att minska förvirringen och öka konsekvensen.   
**Information:** Dokumentmappning i organisationens dokumentation eller i själva Azure Policy definitionen genom att [](../../governance/policy/concepts/definition-structure.md#display-name-and-description) lägga till en referens till organisationsprincipen i principdefinitionen eller [initiativdefinitionens](../../governance/policy/concepts/initiative-definition-structure.md#metadata) beskrivning.

## <a name="monitor-azure-ad-risk-reports"></a>Övervaka Azure AD-riskrapporter
De allra flesta säkerhetsöverträdelser sker när angripare får åtkomst till en miljö genom att stjäla en användares identitet. Det är inte enkelt att identifiera komprometterade identiteter. Azure AD använder anpassningsbara maskininlärningsalgoritmer och heuristik för att identifiera misstänkta åtgärder som är relaterade till dina användarkonton. Varje identifierad misstänkt åtgärd lagras i en post som kallas [riskidentifiering.](../../active-directory/identity-protection/overview-identity-protection.md) Riskidentifiering registreras i Azure AD-säkerhetsrapporter. Mer information finns i [säkerhetsrapporten om användare i riskzonen](../../active-directory/identity-protection/overview-identity-protection.md) och [säkerhetsrapporten för riskfyllda inloggningar.](../../active-directory/identity-protection/overview-identity-protection.md)

## <a name="next-steps"></a>Nästa steg
Se [Metodtips och mönster för säkerhet](best-practices-and-patterns.md) i Azure för fler metodtips för säkerhet som du kan använda när du utformar, distribuerar och hanterar dina molnlösningar med hjälp av Azure.

Följande resurser är tillgängliga för att ge mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Azures säkerhetsteamsblogg](/archive/blogs/azuresecurity/) – för uppdaterad information om det senaste i Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där Microsofts säkerhetsproblem, inklusive problem med Azure, kan rapporteras eller via e-post till secure@microsoft.com