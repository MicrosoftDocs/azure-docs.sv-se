---
title: Rekommenderade säkerhets metoder för dina Azure-tillgångar
titleSuffix: Azure security
description: Den här artikeln innehåller en uppsättning metod tips för att skydda dina data, program och andra till gångar i Azure.
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
ms.openlocfilehash: 86874a60d48ddcbdaca5ae779ad554ee58cc233b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96498854"
---
# <a name="azure-operational-security-best-practices"></a>Metod tips för Azure drift säkerhet
Den här artikeln innehåller en uppsättning metod tips för att skydda dina data, program och andra till gångar i Azure.

Bästa praxis bygger på en uppfattning om yttrandet och de fungerar med aktuella funktioner i Azure-plattformen och-funktions uppsättningar. Åsikter och tekniker förändras med tiden och den här artikeln uppdateras regelbundet för att avspegla ändringarna.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definiera och distribuera starka drift säkerhets metoder
Azures drift säkerhet syftar på tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda data, program och andra till gångar i Azure. Azures operativa säkerhet bygger på ett ramverk som införlivar den kunskap som du har fått via funktioner som är unika för Microsoft, inklusive [livs cykeln för säkerhets utveckling (SDL)](https://www.microsoft.com/sdl), [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) -programmet och djupgående medvetenhet om cybersäkerhet Threat landskap.

## <a name="manage-and-monitor-user-passwords"></a>Hantera och övervaka användar lösen ord
I följande tabell visas några metod tips för att hantera användar lösen ord:

**Bästa praxis**: kontrol lera att du har rätt skydds nivå för lösen ord i molnet.   
**Information**: Följ rikt linjerna i [Microsoft Password guide](https://www.microsoft.com/research/publication/password-guidance/), som är begränsade till användare av Microsoft identity-plattformar (Azure Active Directory, Active Directory och Microsoft-konto).

**Bästa praxis**: övervaka för misstänkta åtgärder relaterade till dina användar konton.   
**Information**: övervaka för [användare i risk](../../active-directory/identity-protection/overview-identity-protection.md) [-och riskfyllda inloggningar](../../active-directory/identity-protection/overview-identity-protection.md) med hjälp av Azure AD-säkerhetsrapporter.

**Bästa praxis**: automatisk identifiering och reparation av lösen ord med hög risk.   
**Information**: [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) är en funktion i Azure AD Premium P2-versionen som gör att du kan:

- Identifiera potentiella sårbarheter som påverkar organisationens identiteter
- Konfigurera automatiska svar på identifierade misstänkta åtgärder som är relaterade till din organisations identiteter
- Undersök misstänkta incidenter och vidta lämpliga åtgärder för att lösa dem

## <a name="receive-incident-notifications-from-microsoft"></a>Ta emot incident meddelanden från Microsoft
Se till att säkerhets åtgärds teamet tar emot Azure incident-aviseringar från Microsoft. En incident avisering gör att ditt säkerhets team vet att du har komprometterat Azure-resurser så att de snabbt kan reagera på och åtgärda potentiella säkerhets risker.

I Azure-registrerings portalen kan du se till att administratörs kontakt information innehåller information som meddelar säkerhets åtgärder. Kontakt information är en e-postadress och ett telefonnummer.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organisera Azure-prenumerationer i hanterings grupper
Om din organisation har många prenumerationer kanske du behöver ett sätt att effektivt hantera åtkomst, principer och efterlevnad för dessa prenumerationer. [Hanterings grupper för Azure](../../governance/management-groups/create-management-group-portal.md) ger en nivå av omfattning som är ovanför prenumerationer. Du ordnar dina prenumerationer i behållare som kallas hanteringsgrupper och tillämpar styrningsvillkor på de olika hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de villkor som tillämpas för hanteringsgruppen.

Du kan bygga en flexibel struktur av hanterings grupper och prenumerationer i en katalog. Varje katalog tilldelas en enda toppnivå hanterings grupp som kallas rot hanterings gruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Rot hanterings gruppen tillåter att globala principer och Azure Role-tilldelningar tillämpas på katalog nivå.

Här följer några metod tips för att använda hanterings grupper:

**Bästa praxis**: se till att nya prenumerationer tillämpar styrnings element som principer och behörigheter när de läggs till.   
**Information**: Använd rot hanterings gruppen för att tilldela företagsomfattande säkerhets element som gäller för alla Azure-tillgångar. Principer och behörigheter är exempel på element.

**Bästa praxis**: justera de högsta nivåerna i hanterings grupper med segmenterings strategi för att tillhandahålla en punkt för kontroll och princip konsekvens i varje segment.   
**Information**: skapa en enda hanterings grupp för varje segment under rot hanterings gruppen. Skapa inte några andra hanterings grupper under roten.

**Bästa praxis**: begränsa hanterings gruppens djup för att undvika förvirring som hindrar både drift och säkerhet.   
**Information**: begränsa hierarkin till tre nivåer, inklusive roten.

**Bästa praxis**: Välj noggrant vilka objekt som ska användas för hela företaget med rot hanterings gruppen.   
**Information**: kontrol lera att rot hanterings gruppens element har en tydlig effekt som måste tillämpas på alla resurser och att de har låg påverkan.

Bra kandidater är:

- Reglerande krav som har en tydlig inverkan på verksamheten (till exempel begränsningar som rör data suveränitet)
- Krav med ett nära noll potentiellt negativt påverkan på åtgärder, till exempel princip med gransknings effekt eller Azure RBAC-behörighet som har granskats noggrant

**Bästa praxis**: planera och testa alla företagsomfattande ändringar i rot hanterings gruppen innan du tillämpar dem (princip, Azure RBAC-modell och så vidare).   
**Information**: ändringar i rot hanterings gruppen kan påverka alla resurser i Azure. Även om de ger ett kraftfullt sätt att garantera konsekvens i företaget kan fel eller felaktig användning påverka produktions åtgärderna negativt. Testa alla ändringar i rot hanterings gruppen i ett test labb eller produktions pilot.

## <a name="streamline-environment-creation-with-blueprints"></a>Effektivisera miljö skapandet med skisser
[Azure](../../governance/blueprints/overview.md) Forms-tjänsten gör det möjligt för moln arkitekter och Central informations teknik grupper att definiera en upprepnings bar uppsättning Azure-resurser som implementerar och följer en organisations standarder, mönster och krav. Azure-ritningar gör det möjligt för utvecklings grupper att snabbt bygga och sätta igång nya miljöer med en uppsättning inbyggda komponenter och det är säkert att de skapar dessa miljöer inom organisationens efterlevnad.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Övervaka lagrings tjänster för oväntade ändringar i beteendet
Det kan vara mer komplicerat att diagnostisera och felsöka problem i ett distribuerat program som finns i en moln miljö än i traditionella miljöer. Program kan distribueras i en PaaS-eller IaaS-infrastruktur, lokalt, på en mobil enhet eller i en kombination av dessa miljöer. Ditt programs nätverks trafik kan passera offentliga och privata nätverk och programmet kan använda flera lagrings tekniker.

Du bör kontinuerligt övervaka de lagrings tjänster som programmet använder för oväntade ändringar i beteendet (till exempel långsammare svars tider). Använd loggning för att samla in mer detaljerade data och analysera ett problem i djupet. Diagnostikinformation som du hämtar från både övervakning och loggning hjälper dig att fastställa rotor saken till problemet som ditt program har påträffat. Sedan kan du felsöka problemet och fastställa lämpliga steg för att åtgärda det.

[Azure-lagringsanalys](../../storage/common/storage-analytics.md) utför loggning och tillhandahåller mått data för ett Azure Storage-konto. Vi rekommenderar att du använder dessa data för att spåra begär Anden, analysera användnings trender och diagnostisera problem med ditt lagrings konto.

## <a name="prevent-detect-and-respond-to-threats"></a>Förhindra, identifiera och svara på hot
[Azure Security Center](../../security-center/security-center-introduction.md) hjälper dig att förhindra, identifiera och svara på hot genom att tillhandahålla ökad insyn i (och kontroll över) säkerheten för dina Azure-resurser. Den ger integrerad säkerhetsövervakning och princip hantering över dina Azure-prenumerationer, hjälper till att upptäcka hot som annars kan gå vidare och fungerar med olika säkerhetslösningar.

Den kostnads fria nivån av Security Center erbjuder begränsad säkerhet för endast dina Azure-resurser. Standard nivån utökar dessa funktioner till lokala och andra moln. Security Center standard hjälper dig att hitta och åtgärda säkerhets problem, använda åtkomst-och program kontroller för att blockera skadlig aktivitet, identifiera hot genom att använda analyser och intelligens och svara snabbt vid angrepp. Du kan prova Security Center Standard utan kostnad under de första 60 dagarna. Vi rekommenderar att du [uppgraderar din Azure-prenumeration till Security Center standard](../../security-center/security-center-get-started.md).

Använd Security Center för att få en central vy över säkerhets läget för alla dina Azure-resurser. Kontrol lera snabbt att rätt säkerhets kontroller är på plats och korrekt konfigurerade och identifiera eventuella resurser som behöver åtgärdas.

Security Center integreras också med [Microsoft Defender Avancerat skydd (ATP)](../../security-center/security-center-wdatp.md), vilket ger omfattande funktioner för slut punkts identifiering och-svar (EDR). Med Microsoft Defender ATP-integrering kan du upptäcka avvikelser. Du kan också identifiera och svara på avancerade attacker på Server slut punkter som övervakas av Security Center.

Nästan alla företags organisationer har ett SIEM-system (Security information and Event Management) för att identifiera nya hot genom att konsolidera logg information från olika data insamlings enheter. Loggarna analyseras sedan av ett data analys system som hjälper dig att identifiera vad som är "intressant" från bruset som är oundvikligt i alla lösningar för insamling och analys av loggar.

[Azure Sentinel](../../sentinel/overview.md) är en skalbar, molnbaserad, molnbaserad, säkerhets informations-och händelse hantering (Siem) och Soar-lösning (Security Orchestration autoresponse). Azure Sentinel tillhandahåller intelligent säkerhets analys och hot information via aviserings identifiering, Hot synlighet, proaktiv jakt och automatiserat hot svar.

Här följer några metod tips för att förebygga, upptäcka och svara på hot:

**Bästa praxis**: öka hastigheten och skalbarheten för din SIEM-lösning med hjälp av en molnbaserad Siem.   
**Information**: Undersök funktionerna i [Azure Sentinel](../../sentinel/overview.md) och jämför dem med funktionerna i vad du använder lokalt. Överväg att införa Azure Sentinel om det uppfyller organisationens krav på SIEM.

**Bästa praxis**: hitta de allvarliga säkerhets sårbarheterna så att du kan prioritera undersökningen.   
**Information**: granska dina [Azures säkra Poäng](../../security-center/secure-score-security-controls.md) för att se rekommendationerna från de Azure-principer och-initiativ som är inbyggda i Azure Security Center. Dessa rekommendationer hjälper dig att lösa de vanligaste riskerna som säkerhets uppdateringar, Endpoint Protection, kryptering, säkerhetskonfigurationer, saknade WAF, Internet-anslutna virtuella datorer och många fler.

De säkra poängen, som baseras på CIS-kontroller (Center for Internet Security), gör det möjligt att mäta organisationens Azure-säkerhet mot externa källor. Extern verifiering hjälper till att validera och utöka ditt teams säkerhets strategi.

**Bästa praxis**: övervaka säkerhets position för datorer, nätverk, lagrings-och data tjänster och program för att identifiera och prioritera potentiella säkerhets problem.  
**Information**: Följ [säkerhets rekommendationerna](../../security-center/security-center-recommendations.md) i Security Center som startar med högst prioritets objekt.

**Bästa praxis**: integrera Security Center aviseringar i din lösning för säkerhets information och händelse hantering (Siem).   
**Information**: de flesta organisationer med en Siem använder den som en central Clearinghouse för säkerhets aviseringar som kräver en analytikers svar. Bearbetade händelser som genereras av Security Center publiceras i Azure aktivitets loggen, en av loggarna som är tillgängliga via Azure Monitor. Azure Monitor erbjuder en konsol IDE rad pipeline för att vidarebefordra dina övervaknings data till ett SIEM-verktyg. Instruktioner finns i [Stream-aviseringar till en Siem, Soar eller IT Service Management-lösning](../../security-center/export-to-siem.md) . Om du använder Azure Sentinel, se [anslut Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Bästa praxis**: integrera Azure-loggar med din Siem.   
**Information**: Använd [Azure Monitor för att samla in och exportera data](../../azure-monitor/overview.md#integrate-and-export-data). Den här metoden är viktig för att aktivera undersökningen av säkerhets incidenter och logg kvarhållning online är begränsad. Om du använder Azure Sentinel, se [Anslut data källor](../../sentinel/connect-data-sources.md).

**Bästa praxis**: påskynda dina undersökningar och jakt processer och minska antalet falska positiva identifieringar genom att integrera funktionerna för slut punkts identifiering och-svar (EDR) i angrepps undersökningen.   
**Information**: [aktivera Microsoft Defender för slut punkts integrering](../../security-center/security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration) via din Security Center säkerhets princip. Överväg att använda Azure Sentinel för hotets jakt och incident svar.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Övervaka övervakning från slut punkt till slut punkt för scenario baserad nätverks övervakning
Kunderna skapar ett nätverk från slut punkt till slut punkt i Azure genom att kombinera nätverks resurser som ett virtuellt nätverk, ExpressRoute, Application Gateway och belastningsutjämnare. Övervakning är tillgängligt på varje nätverks resurs.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) är en regional tjänst. Använd dess diagnostik-och visualiserings verktyg för att övervaka och diagnostisera villkor på en nätverks scenario nivå i, till och från Azure.

Följande är metod tips för nätverks övervakning och tillgängliga verktyg.

**Bästa praxis**: automatisera övervakning av fjärrnätverk med paket fångst.  
**Information**: övervaka och diagnostisera nätverks problem utan att logga in på dina virtuella datorer med hjälp av Network Watcher. Utlös [paket insamling](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) genom att ställa in aviseringar och få till gång till prestanda information i real tid på paket nivå. När du ser ett problem kan du undersöka i detalj för bättre diagnostisering.

**Bästa praxis**: få insikt i din nätverks trafik genom att använda flödes loggar.  
**Information**: Bygg en djupare förståelse av dina nätverks trafik mönster genom att använda [flödes loggar för nätverks säkerhets grupper](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). Information i flödes loggar hjälper dig att samla in data för efterlevnad, granskning och övervakning av nätverks säkerhets profilen.

**Bästa praxis**: diagnostisera problem med VPN-anslutningen.  
**Information**: Använd Network Watcher för att [diagnostisera dina vanligaste VPN gateway-och anslutnings problem](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Du kan inte bara identifiera problemet utan också använda detaljerade loggar för ytterligare undersökning.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Säker distribution med hjälp av beprövade DevOps-verktyg
Använd följande metod tips för DevOps för att se till att ditt företag och dina team är produktiva och effektiva.

**Bästa praxis**: automatisera bygge och distribution av tjänster.  
**Information**: [infrastruktur som kod](/azure/devops/learn/what-is-infrastructure-as-code) är en uppsättning tekniker och metoder som hjälper IT-tekniker att ta bort belastningen på den dagliga utvecklaren och hanteringen av modulär infrastruktur. Det gör det möjligt för IT-proffs att bygga och underhålla sin moderna Server miljö på ett sätt som liknar hur programutvecklare bygger och underhåller program koden.

Du kan använda [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) för att etablera dina program med hjälp av en deklarativ mall. I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall för att distribuera programmet flera gånger i varje skede av programmets livs cykel.

**Bästa praxis**: att bygga och distribuera automatiskt till Azure-webbappar eller moln tjänster.  
**Information**: du kan konfigurera Azure DevOps Projects att  [automatiskt bygga och distribuera](/azure/devops/pipelines/index) till Azure-webbappar eller moln tjänster. Azure DevOps distribuerar automatiskt binärfilerna när du har gjort en build till Azure efter varje kod incheckning. Paket Bygg processen motsvarar paket kommandot i Visual Studio och publicerings stegen motsvarar kommandot Publicera i Visual Studio.

**Bästa praxis**: automatiserad versions hantering.  
**Information**: [Azure-pipeline](/azure/devops/pipelines/index) är en lösning för automatisering av distribution av flera steg och hantering av versions processen. Skapa hanterade pipeliner för kontinuerlig distribution för att få ut snabbt, enkelt och ofta. Med Azure-pipelines kan du automatisera din versions process och du kan ha fördefinierade arbets flöden för godkännande. Distribuera lokalt och i molnet, utöka och anpassa efter behov.

**Bästa praxis**: kontrol lera appens prestanda innan du startar den eller distribuerar uppdateringar till produktion.  
**Information**: kör molnbaserad [belastnings test](/azure/devops/test/load-test/overview#alternatives) för att:

- Hitta prestanda problem i din app.
- Förbättra distributions kvaliteten.
- Kontrol lera att din app alltid är tillgänglig.
- Kontrol lera att din app kan hantera trafik för nästa lansering eller marknadsförings kampanj.

[Apache jmeter](https://jmeter.apache.org/) är ett kostnads fritt, populärt verktyg för öppen källkod med en stark community-återställning.

**Bästa praxis**: övervaka program prestanda.  
**Information**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) är en utöknings bar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Använd Application Insights för att övervaka ditt Live-webbprogram. Prestanda avvikelser identifieras automatiskt. Den innehåller analys verktyg som hjälper dig att diagnostisera problem och förstå vad användare faktiskt gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

## <a name="mitigate-and-protect-against-ddos"></a>Minimera och skydda mot DDoS
Distributed denial of service (DDoS) är en typ av attack som försöker leverera program resurser. Målet är att påverka programmets tillgänglighet och dess förmåga att hantera legitima begär Anden. Dessa attacker blir mer sofistikerade och större i storlek och påverkan. De kan riktas mot vilken slut punkt som helst som är offentligt tillgänglig via Internet.

Design och byggnad för DDoS-återhämtning kräver planering och design för en mängd olika fellägen. Nedan följer metod tips för att skapa DDoS-elastiska tjänster på Azure.

**Bästa praxis**: se till att säkerheten är en prioritet under hela livs cykeln för ett program, från design och implementering till distribution och drift. Program kan ha buggar som tillåter en relativt låg mängd begär Anden att använda mycket resurser, vilket resulterar i ett avbrott i tjänsten.  
**Information**: för att skydda en tjänst som körs på Microsoft Azure bör du ha en god förståelse för din program arkitektur och fokusera på de [fem pelaren för program kvalitet](/azure/architecture/guide/pillars). Du bör känna till vanliga trafik volymer, anslutnings modellen mellan programmet och andra program och de tjänst slut punkter som exponeras för det offentliga Internet.

Att se till att ett program är tillräckligt flexibelt för att hantera en denial of service som är riktad mot själva programmet är viktigast. Säkerhet och sekretess är inbyggda i Azure-plattformen, från och med [säkerhets utvecklings livs cykeln (SDL)](https://www.microsoft.com/sdl). SDL adresserar säkerheten i varje utvecklings fas och säkerställer att Azure uppdateras kontinuerligt för att göra det ännu säkrare.

**Bästa praxis**: utforma dina program så att de kan [skalas horisontellt](/azure/architecture/guide/design-principles/scale-out) för att möta efter frågan på en förstärkt belastning, särskilt i händelse av en DDoS-attack. Om ditt program är beroende av en enda instans av en tjänst skapas en enskild felpunkt. Genom att tillhandahålla flera instanser blir systemet mer flexibelt och mer skalbart.  
**Information**: för [Azure App Service](../../app-service/overview.md)väljer du en [App Service plan](../../app-service/overview-hosting-plans.md) som erbjuder flera instanser.

För Azure Cloud Services konfigurerar du var och en av dina roller så att de använder [flera instanser](../../cloud-services/cloud-services-choose-me.md).

För [Azure Virtual Machines](../../virtual-machines/windows/overview.md)kontrollerar du att din virtuella dator arkitektur innehåller fler än en virtuell dator och att varje virtuell dator ingår i en [tillgänglighets uppsättning](../../virtual-machines/windows/tutorial-availability-sets.md). Vi rekommenderar att du använder skalnings uppsättningar för virtuella datorer för funktioner för automatisk skalning.

**Bästa praxis**: säkerhets försvars nivåer i ett program minskar risken för en lyckad attack. Implementera säkra utformningar för dina program med hjälp av de inbyggda funktionerna i Azure-plattformen.  
**Detaljer**: risken för attacker ökar med programmets storlek (arean). Du kan minska arean genom att använda en godkännande lista för att stänga det exponerade IP-adressutrymmet och lyssnande portar som inte behövs på belastningsutjämnaren ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) och [Azure Application Gateway](../../application-gateway/application-gateway-create-probe-portal.md)).

[Nätverks säkerhets grupper](../../virtual-network/network-security-groups-overview.md) är ett annat sätt att minska angrepps ytan. Du kan använda [tjänst Taggar](../../virtual-network/network-security-groups-overview.md#service-tags) och [program säkerhets grupper](../../virtual-network/network-security-groups-overview.md#application-security-groups) för att minimera komplexiteten för att skapa säkerhets regler och konfigurera nätverks säkerhet som ett naturligt tillägg till ett programs struktur.

Du bör Distribuera Azure-tjänster i ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) närhelst det är möjligt. Den här metoden gör det möjligt för tjänst resurser att kommunicera via privata IP-adresser. Azure Service-trafik från ett virtuellt nätverk använder offentliga IP-adresser som standard käll-IP-adresser.

Genom att använda [tjänst slut punkter](../../virtual-network/virtual-network-service-endpoints-overview.md) växlar tjänst trafiken till att använda privata IP-adresser som käll-IP-adresser när de använder Azure-tjänsten från ett virtuellt nätverk.

Vi ser ofta kunders lokala resurser som tar emot angrepp tillsammans med sina resurser i Azure. Om du ansluter en lokal miljö till Azure minimerar du exponeringen för lokala resurser till det offentliga Internet.

Azure har två DDoS [tjänst erbjudanden](../../ddos-protection/ddos-protection-overview.md) som ger skydd mot nätverks attacker:

- Grundläggande skydd integreras i Azure som standard utan extra kostnad. Skalan och kapaciteten för det globalt distribuerade Azure-nätverket ger skydd mot vanliga attacker på nätverks nivå genom att alltid övervaka trafik övervakning och i real tid. Basic kräver ingen användar konfiguration eller program ändringar och hjälper till att skydda alla Azure-tjänster, inklusive PaaS-tjänster som Azure DNS.
- Med standard skydd får du avancerade funktioner för DDoS-minskning mot nätverks attacker. Det är automatiskt anpassat för att skydda dina Azure-resurser. Det är enkelt att aktivera skydd när du skapar virtuella nätverk. Du kan också göra det när det har skapats och inga program-eller resurs ändringar krävs.

## <a name="enable-azure-policy"></a>Aktivera Azure Policy
[Azure policy](../../governance/policy/overview.md) är en tjänst i Azure som du använder för att skapa, tilldela och hantera principer. Dessa principer tillämpar regler och effekter över dina resurser, så att resurserna följer företagets standarder och service nivå avtal. Azure Policy gör detta genom att utvärdera resursernas kompatibilitet med hjälp av tilldelade principer.

Aktivera Azure Policy för att övervaka och upprätthålla din organisations skriftliga princip. Detta säkerställer efterlevnaden av företagets eller regelns säkerhets krav genom att centralt hantera säkerhets principer i dina hybrid moln arbets belastningar. Lär dig hur du [skapar och hanterar principer för att genomdriva efterlevnad](../../governance/policy/tutorials/create-and-manage.md). Se [Azure policy definitions struktur](../../governance/policy/concepts/definition-structure.md) för en översikt över elementen i en princip.

Här följer några rekommenderade säkerhets metoder när du har infört Azure Policy:

**Bästa praxis**: principen stöder flera typer av effekter. Du kan läsa om dem i [Azure policy definitions struktur](../../governance/policy/concepts/definition-structure.md#policy-rule). Affärs åtgärder kan påverkas negativt av återställnings **effekten och** den **åtgärd** som tillämpas, så börja med **gransknings** effekten för att begränsa risken för negativ påverkan från principen.   
**Information**: [Starta princip distributioner i gransknings läge](../../governance/policy/concepts/definition-structure.md#policy-rule) och fortsätt sedan till **neka** eller **Reparera**. Testa och granska resultatet av gransknings effekterna innan du går vidare till **neka** eller **åtgärda**.

Mer information finns i [skapa och hantera principer för att genomdriva efterlevnad](../../governance/policy/tutorials/create-and-manage.md).

**Bästa praxis**: identifiera de roller som ansvarar för övervakning av princip överträdelser och se till att rätt reparations åtgärd vidtas snabbt.   
**Information**: ha den tilldelade rollen övervaka efterlevnaden via [Azure Portal](../../governance/policy/how-to/get-compliance-data.md#portal) eller via [kommando raden](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Bästa praxis**: Azure policy är en teknisk åter givning av en organisations skriftliga principer. Mappa alla Azure Policys definitioner till organisations principer för att minska förvirring och öka konsekvens.   
**Information**: dokument mappning i din organisations dokumentation eller i själva Azure policy definitionen genom att lägga till en referens till organisations principen i [princip definitionen](../../governance/policy/concepts/definition-structure.md#display-name-and-description) eller beskrivningen av [initiativ definitionen](../../governance/policy/concepts/initiative-definition-structure.md#metadata) .

## <a name="monitor-azure-ad-risk-reports"></a>Övervaka Azure AD-riskfyllda rapporter
De flesta säkerhets överträdelser sker när angripare får åtkomst till en miljö genom att stjäla en användares identitet. Det är ingen enkel uppgift att identifiera komprometterade identiteter. Azure AD använder anpassningsbara algoritmer för maskin inlärning och heuristik för att identifiera misstänkta åtgärder som är relaterade till dina användar konton. Varje misstänkt misstänkt åtgärd lagras i en post som kallas för [identifiering av risker](../../active-directory/identity-protection/overview-identity-protection.md). Identifieringar av risker registreras i Azure AD-säkerhetsrapporter. Mer information finns i artikeln om säkerhets [rapporter för användare](../../active-directory/identity-protection/overview-identity-protection.md) och [riskfyllda inloggningar](../../active-directory/identity-protection/overview-identity-protection.md).

## <a name="next-steps"></a>Nästa steg
Se [metod tips och mönster för Azure-säkerhet](best-practices-and-patterns.md) för att få bättre säkerhets metoder att använda när du utformar, distribuerar och hanterar dina moln lösningar med hjälp av Azure.

Följande resurser är tillgängliga för att ge mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Azure Security Team-bloggen](/archive/blogs/azuresecurity/) – för uppdaterad information om det senaste i Azure-säkerhet
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där säkerhets problem i Microsoft, inklusive problem med Azure, kan rapporteras eller via e-post till secure@microsoft.com