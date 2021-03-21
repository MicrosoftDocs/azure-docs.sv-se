---
title: Azure Threat Protection | Microsoft Docs
description: Lär dig mer om inbyggda hot skydds funktioner i Azure, till exempel tjänsten Azure AD Identity Protection.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 81b2227ac519de4d03bb63907d14c23b9c34dc64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101043"
---
# <a name="azure-threat-protection"></a>Azure Threat Protection

Azure erbjuder inbyggd hot skydds funktion via tjänster som Azure Active Directory (Azure AD), Azure Monitor loggar och Azure Security Center. Den här samlingen av säkerhets tjänster och funktioner är ett enkelt och snabbt sätt att förstå vad som händer i dina Azure-distributioner.

Azure erbjuder ett brett utbud av alternativ för att konfigurera och anpassa säkerheten för att uppfylla kraven för dina program distributioner. Den här artikeln beskriver hur du uppfyller dessa krav.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) är en [Azure Active Directory Premium P2](../../active-directory/fundamentals/active-directory-whatis.md) Edition-funktion som ger en översikt över risk identifiering och potentiella sårbarheter som kan påverka organisationens identiteter. Identitets skydd använder befintliga funktioner för identifiering av Azure AD-avvikelse som är tillgängliga via [rapporter om avvikande rapporter i Azure AD](../../active-directory/reports-monitoring/overview-reports.md)och introducerar nya risk identifierings typer som kan identifiera real tids avvikelser.

![Azure AD Identity Protection diagram](./media/threat-detection/azure-threat-detection-fig1.png)

Identitets skydd använder anpassningsbara algoritmer för maskin inlärning och heuristik för att identifiera avvikelser och risk identifieringar som kan tyda på att en identitet har komprometterats. Med hjälp av dessa data genererar identitets skydd rapporter och aviseringar så att du kan undersöka dessa risk identifieringar och vidta lämpliga åtgärder för att åtgärda problemet.

Azure Active Directory Identity Protection är mer än ett övervaknings-och rapporterings verktyg. Baserat på risk identifieringar beräknar Identity Protection en användar risk nivå för varje användare, så att du kan konfigurera riskfyllda principer för att automatiskt skydda organisationens identiteter.

Dessa riskbaserade principer, förutom andra [villkorliga åtkomst kontroller](../../active-directory/conditional-access/overview.md) som tillhandahålls av Azure Active Directory och [EMS](../../active-directory/conditional-access/overview.md), kan automatiskt blockera eller erbjuda anpassningsbara åtgärds åtgärder som inkluderar lösen ords återställning och Multi-Factor Authentication-tvång.

### <a name="identity-protection-capabilities"></a>Funktioner för identitets skydd

Azure Active Directory Identity Protection är mer än ett övervaknings-och rapporterings verktyg. För att skydda din organisations identiteter kan du konfigurera riskfyllda principer som automatiskt svarar på identifierade problem när en angiven risk nivå har uppnåtts. Dessa principer, förutom andra villkorliga åtkomst kontroller som tillhandahålls av Azure Active Directory och EMS, kan antingen automatiskt blockera eller initiera anpassningsbara åtgärds åtgärder, inklusive lösen ords återställning och Multi-Factor Authentication-tvång.

Exempel på några av de sätt som Azure Identity Protection kan hjälpa till att skydda dina konton och identiteter är:

[Identifiera risk identifieringar och riskfyllda konton](../../active-directory/identity-protection/overview-identity-protection.md)
-   Identifiera sex risk identifierings typer med hjälp av maskin inlärnings-och heuristiska regler.
-   Beräkna risk nivåer för användare.
-   Ange anpassade rekommendationer för att förbättra den övergripande säkerhets position genom att markera sårbarheter.

[Undersöka risk identifieringar](../../active-directory/identity-protection/overview-identity-protection.md)
-   Skicka meddelanden om risk identifieringar.
-   Undersök risk identifieringar med hjälp av relevant och sammanhangsbaserad information.
-   Ange grundläggande arbets flöden för att spåra utredningar.
-   Ger enkel åtkomst till reparations åtgärder som återställning av lösen ord.

[Riskfyllda principer för villkorlig åtkomst](../../active-directory/identity-protection/overview-identity-protection.md)
-   Minimera riskfyllda inloggningar genom att blockera inloggningar eller kräva Multi-Factor Authentication-utmaningar.
-   Blockera eller skydda riskfyllda användar konton.
-   Kräv att användare registrerar sig för Multi-Factor Authentication.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Med [Azure Active Directory Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md)kan du hantera, kontrol lera och övervaka åtkomst i din organisation. Den här funktionen inkluderar åtkomst till resurser i Azure AD och andra Microsoft-onlinetjänster, till exempel Microsoft 365 eller Microsoft Intune.

![Azure AD Privileged Identity Management diagram](./media/threat-detection/azure-threat-detection-fig2.png)

PIM hjälper dig att:

-   Få aviseringar och rapporter om Azure AD-administratörer och just-in-Time (JIT) administrativ åtkomst till Microsoft onlinetjänster, till exempel Microsoft 365 och Intune.

-   Hämta rapporter om administratörs åtkomst historik och ändringar i administratörs tilldelningar.

-   Få aviseringar om åtkomst till en privilegie rad roll.

## <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor loggar](../../azure-monitor/index.yml) är en molnbaserad lösning för IT-hantering i Microsoft som hjälper dig att hantera och skydda din lokala infrastruktur och moln infrastruktur. Eftersom Azure Monitor-loggar implementeras som en molnbaserad tjänst, kan du snabbt få igång med minimal investering i infrastruktur tjänster. Nya säkerhetsfunktioner levereras automatiskt och sparar löpande underhålls-och uppgraderings kostnader.

Förutom att tillhandahålla värdefulla tjänster på egen hand kan Azure Monitor loggar integreras med System Center-komponenter, till exempel [System Center Operations Manager](/archive/blogs/cbernier/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started), för att utöka dina befintliga investeringar i hantering av säkerhet till molnet. System Center och Azure Monitor loggar kan samar beta för att ge en fullständig hybrid hanterings upplevelse.

### <a name="holistic-security-and-compliance-posture"></a>Holistisk position för säkerhet och efterlevnad

[Azure Security Center](../../security-center/security-center-introduction.md) ger en omfattande översikt över din organisations IT-position, med inbyggda Sök frågor för viktiga problem som kräver din uppmärksamhet. Den innehåller en översikt över säkerhetsstatusen för dina datorer. Du kan också Visa alla händelser från de senaste 24 timmarna, 7 dagar eller andra anpassade tids ramar.

Azure Monitor loggar hjälper dig att snabbt och enkelt förstå den övergripande säkerhets position i alla miljöer, allt inom ramen för IT-åtgärder, inklusive utvärdering av program uppdateringar, utvärdering av program mot skadlig kod och konfigurations bas linjer. Säkerhets logg data är lätt att komma åt för att effektivisera gransknings processerna för säkerhet och efterlevnad.

### <a name="insight-and-analytics"></a>Insight and Analytics
I mitten av [Azure Monitor loggar](../../azure-monitor/logs/log-query-overview.md) är lagrings platsen som finns i Azure.

![Diagram över insikter och analys](./media/threat-detection/azure-threat-detection-fig4.png)

Du samlar in data i databasen från anslutna källor genom att konfigurera data källor och lägga till lösningar i din prenumeration.

Data källor och lösningar varje skapa separata post typer med en egen uppsättning egenskaper, men du kan fortfarande analysera dem tillsammans i frågor till lagrings platsen. Du kan använda samma verktyg och metoder för att arbeta med en mängd data som samlas in av olika källor.

De flesta av dina interaktioner med Azure Monitor loggar sker via Azure Portal, som körs i alla webbläsare och ger åtkomst till konfigurations inställningar och flera verktyg för att analysera och agera på insamlade data. Från portalen kan du använda:
* [Loggs ökningar](../../azure-monitor/logs/log-query-overview.md) där du skapar frågor för att analysera insamlade data.
* [Instrument paneler](../../azure-monitor/visualize/tutorial-logs-dashboards.md), som du kan anpassa med grafiska vyer över dina mest värdefulla sökningar.
* [Lösningar](../../azure-monitor/insights/solutions.md)som ger ytterligare funktioner och analys verktyg.

Lösningar lägger till funktioner i Azure Monitor loggar. De körs främst i molnet och tillhandahåller analys av data som samlas in i Log Analytics-lagringsplatsen. Lösningar kan också definiera nya post typer som ska samlas in som kan analyseras med loggs ökningar eller med hjälp av ett ytterligare användar gränssnitt som lösningen tillhandahåller i Log Analytics-instrumentpanelen.

Security Center är ett exempel på dessa typer av lösningar.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatisering och kontroll: avisering om säkerhets konfigurations avvikelser

Azure Automation automatiserar administrativa processer med Runbooks som baseras på PowerShell och som körs i molnet. Runbooks kan också köras på en server i ditt lokala datacenter för att hantera lokala resurser. Azure Automation tillhandahåller konfigurations hantering med PowerShell (Desired State Configuration).

![Azure Automation diagram](./media/threat-detection/azure-threat-detection-fig7.png)

Du kan skapa och hantera DSC-resurser som finns i Azure och tillämpa dem på molnet och lokala system. Genom att göra det kan du definiera och tillämpa konfigurationen automatiskt eller få rapporter om driften för att se till att säkerhetskonfigurationerna finns kvar i principen.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center skyddar din hybrid moln miljö. Genom att utföra kontinuerliga säkerhets utvärderingar av dina anslutna resurser kan du ange detaljerade säkerhets rekommendationer för de identifierade säkerhets problemen.

Security Centers rekommendationer baseras på [Azures säkerhets riktmärke](../benchmarks/introduction.md) – den Microsoft-skapade, Azure-/regionsspecifika uppsättningen rikt linjer för säkerhets-och efterlevnads metod tips som baseras på vanliga ramverk för efterlevnad. Detta respekterade benchmark-versioner bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) med fokus på molnbaserad säkerhet i molnet.

Security Center s integrerade Cloud Worker Protection Platform (CWPP), **Azure Defender**, ger avancerad, intelligent, skydd av dina Azure-och hybrid resurser och arbets belastningar. Genom att aktivera Azure Defender får du en rad ytterligare säkerhetsfunktioner (se [Introduktion till Azure Defender](../../security-center/azure-defender.md)). Azure Defender-instrumentpanelen i Security Center ger insyn och kontroll över CWP-funktionerna i din miljö:

:::image type="content" source="../../security-center/media/azure-defender/sample-defender-dashboard.png" alt-text="Ett exempel på Azure Defender-instrumentpanelen" lightbox="../../security-center/media/azure-defender/sample-defender-dashboard.png":::

Microsofts säkerhetsforskare söker hela tiden efter hot. De har tillgång till en omfattande uppsättning telemetri från Microsofts globala närvaro i molnet och i lokala infrastrukturer. Med den här omfattande och mångfaldiga samlingen data kan Microsoft identifiera nya angreppsmönster och trender i lokala konsument- och företagsprodukter, och i onlinetjänster.

Det innebär att Security Center snabbt kan uppdatera sina detektions algoritmer när angripare släpper nya och allt mer avancerade utnyttjande. Den här metoden hjälper dig att hålla jämna steg med en snabb och rörlig hot miljö.

:::image type="content" source="../../security-center/media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center säkerhets aviserings listan":::

Azure Defender samlar automatiskt in säkerhets information från dina resurser, nätverket och anslutna partner lösningar. Den analyserar informationen, som korrelerar information från flera källor, för att identifiera hot.

Azure Defender-aviseringar prioriteras i Security Center tillsammans med rekommendationer om hur du kan åtgärda hoten.

Security Center använder avancerade säkerhetsanalyser, som går mycket längre än signaturbaserade lösningar. Ban brytande i Big data och [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) -tekniker används för att utvärdera händelser i hela molnet. Avancerad analys kan upptäcka hot som kan vara omöjliga att identifiera genom manuella metoder och förutsäga utvecklingen av attacker. Dessa typer av säkerhets analys beskrivs i nästa avsnitt.

### <a name="threat-intelligence"></a>Hotinformation

Microsoft har till gång till en stor mängd global Hot information.

Telemetri flödar från flera källor, till exempel Azure, Microsoft 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital brottslighet Unit (DCU) och Microsoft Security Response Center (MSRC).

![Hot informations resultat](./media/threat-detection/azure-threat-detection-fig10.jpg)

Forskare får också information om hot information som delas mellan större leverantörer av moln tjänster och de prenumererar på hot information-flöden från tredje part. Azure Security Center kan använda den här informationen för att varna dig om hot från kända illvilliga aktörer. Några exempel är:

-   **Utnyttjar maskin inlärnings kraften**: Azure Security Center har till gång till en stor mängd data om moln nätverks aktivitet som kan användas för att identifiera hot som riktar sig mot dina Azure-distributioner.

-   **Brute Force-identifiering**: Machine Learning används för att skapa ett historiskt mönster med fjärråtkomster, vilket gör det möjligt att identifiera brute force-attacker mot SSH (Secure Shell), Remote Desktop Protocol (RDP) och SQL-portar.

-   **Utgående DDoS-och botnät-identifiering**: ett gemensamt mål för angrepp som riktar sig mot moln resurser är att använda beräknings kraften hos dessa resurser för att köra andra attacker.

-   **Nya beteende analys servrar och virtuella datorer**: när en server eller virtuell dator har komprometterats använder angriparen en mängd olika tekniker för att köra skadlig kod på systemet samtidigt som de kan undvika identifiering, säkerställa Persistens och slipper säkerhets kontroller.

-   **Azure SQL Database hot identifiering**: Hot identifiering för Azure SQL Database, som identifierar avvikande databas aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

### <a name="behavioral-analytics"></a>Beteendeanalys

Beteendeanalys är en teknik som analyserar och jämför data med en samling kända mönster. Dessa mönster är dock inte enkla signaturer. De fastställs genom komplexa maskininlärningsalgoritmer som tillämpas på enorma datamängder,

![Resultat av beteende analys](./media/threat-detection/azure-threat-detection-fig11.jpg)

Mönstren fastställs också genom noggrann analys av skadliga beteenden av expert analyser. Azure Security Center kan använda beteende analys för att identifiera komprometterade resurser baserat på analyser av loggar för virtuella datorer, loggar för virtuella nätverks enheter, infrastruktur loggar, krasch dum par och andra källor.

Dessutom korreleras mönster med andra signaler för att kontrol lera om det finns stödjande bevis för en omfattande kampanj. Den här korrelationen gör det möjligt att identifiera händelser som matchar fastställda hotindikatorer.

Några exempel är:
-   **Misstänkt körning av processer**: Angripare använder flera metoder för att köra skadlig programvara utan identifiering. En angripare kan till exempel ge skadlig kod samma namn som legitima systemfiler, men placera filerna på en annan plats, använda ett namn som liknar det för en ofarlig fil eller maskera filens sanna tillägg. Security Center modeller bearbetar beteenden och övervakar process körningar för att identifiera avvikande värden som dessa.

-   **Dolda skadliga program och användnings försök**: avancerad skadlig kod kan undvika vanliga produkter för program mot skadlig kod genom att antingen aldrig skriva till disk eller kryptera program varu komponenter som lagras på disk. Sådan skadlig kod kan dock identifieras med hjälp av minnes analyser eftersom den skadliga koden måste lämna spår i minnet för att fungera. När programvara kraschar fångar en kraschdumpfil en del av minnet vid tidpunkten för kraschen. Genom att analysera minnet i krasch dumpningen kan Azure Security Center identifiera tekniker som används för att utnyttja sårbarheter i program vara, komma åt konfidentiella data och ligger gömda kvar på en komprometterad dator utan att påverka datorns prestanda.

-   **Lateral förflyttning och internt rekognosering**: för att bevaras i ett komprometterat nätverk och för att hitta och skörda värdefulla data försöker angripare ofta flytta senare från den komprometterade datorn till andra inom samma nätverk. Security Center övervakar process-och inloggnings aktiviteter för att identifiera försök att expandera en angripares fäste i nätverket, till exempel fjärrkörning av kommando, nätverks avsökning och konto uppräkning.

-   **Skadliga PowerShell-skript**: PowerShell kan användas av angripare för att köra skadlig kod på virtuella mål datorer i olika syfte. Security Center inspekterar PowerShell-aktivitet för att hitta tecken på misstänkt aktivitet.

-   **Utgående attacker**: Angripare attackerar ofta resurser i molnet med avsikten att använda dessa resurser för att bygga upp fler attacker. Komprometterade virtuella datorer kan till exempel användas för att starta brute force-attacker mot andra virtuella datorer, skicka skräp post eller skanna öppna portar och andra enheter på Internet. Genom att använda maskininlärning för nätverkstrafiken kan Security Center upptäcka avvikande utgående nätverkskommunikation. När skräp post identifieras korrelerar Security Center också ovanlig e-posttrafik med information från Microsoft 365 för att avgöra om e-postmeddelandet är sannolikt post eller resultatet av en legitim e-postkampanj.

### <a name="anomaly-detection"></a>Avvikelseidentifiering

Azure Security Center använder också avvikelseidentifiering för att identifiera hot. Till skillnad mot beteendeanalyser (som är beroende av kända mönster som härleds från stora datamängder) är avvikelseidentifiering mer ”anpassad” och utgår från standarder som är specifika för dina distributioner. Machine Learning används för att fastställa normal aktivitet för dina distributioner och regler skapas för att definiera avvikare-villkor som kan representera en säkerhets händelse. Här är ett exempel:

-   **Inkommande RDP/SSH-Force-angrepp**: dina distributioner kan ha upptagna virtuella datorer med många inloggningar varje dag och andra virtuella datorer som har några, om det finns några, inloggningar. Azure Security Center kan fastställa bas linje inloggnings aktivitet för dessa virtuella datorer och använda Machine Learning för att definiera för normala inloggnings aktiviteter. Om det finns en avvikelse med den bas linje som definierats för inloggnings egenskaper kan en avisering genereras. Maskininlärningen avgör vad som är viktigt.

### <a name="continuous-threat-intelligence-monitoring"></a>Kontinuerlig övervakning av hotinformation

Azure Security Center arbetar med forsknings-och data vetenskaps team i hela världen som kontinuerligt övervakar för förändringar i hotets landskap. Bland annat kan följande projekt nämnas:

-   **Övervakning av hot information**: Hot information innehåller mekanismer, indikatorer, effekter och åtgärds bara råd om befintliga eller nya hot. Den här informationen delas i säkerhets-communityn och Microsoft övervakar kontinuerligt hot informations flöden från interna och externa källor.

-   **Signal delning**: insikter från säkerhets team i hela Microsoft-portföljen av molnet och lokala tjänster, servrar och klient slut punkts enheter delas och analyseras.

-   **Microsoft-säkerhetsspecialister**: fort löp ande engagemang med team över Microsoft som arbetar i specialiserade säkerhets fält, till exempel data utredning och identifiering av webb attacker.

-   **Identifierings justering**: algoritmer körs mot verkliga kund data uppsättningar och säkerhets forskare arbetar tillsammans med kunderna för att verifiera resultaten. Sann och falsk positiv identifiering används för att förfina maskininlärningsalgoritmerna.

Dessa kombinerade ansträngningar resulterar i nya och förbättrade identifieringar, som du kan dra nytta av direkt. Du kan inte vidta några åtgärder.

## <a name="threat-protection-features-other-azure-services"></a>Hot skydds funktioner: andra Azure-tjänster

### <a name="virtual-machines-microsoft-antimalware"></a>Virtuella datorer: Microsoft Antimalware

[Microsoft Antimalware](antimalware.md) för Azure är en lösning för en lösning för program-och klient miljöer som är utformad för att köras i bakgrunden utan mänsklig inblandning. Du kan distribuera skydd baserat på dina arbets belastningar för dina program, med antingen grundläggande säkerhet eller avancerad anpassad konfiguration, inklusive övervakning av program mot skadlig kod. Azure antimalware är ett säkerhets alternativ för virtuella Azure-datorer som installeras automatiskt på alla virtuella Azure PaaS-datorer.

#### <a name="microsoft-antimalware-core-features"></a>Kärn funktioner i Microsoft Antimalware

Här följer de funktioner i Azure som distribuerar och aktiverar Microsoft Antimalware för dina program:

-   **Real tids skydd**: övervakar aktivitet i moln tjänster och på virtuella datorer för att identifiera och blockera körning av skadlig kod.

-   **Schemalagd genomsökning**: utför regelbundet genomsökning för att identifiera skadlig kod, inklusive aktivt aktiva program.

-   **Reparation av skadlig kod**: fungerar automatiskt vid identifierad skadlig kod, som att ta bort eller sätta skadliga filer och rensa skadliga register poster.

-   **Uppdatering av signaturer**: installerar automatiskt de senaste skydds signaturerna (virus definitioner) för att säkerställa att skyddet är uppdaterat på en i förväg bestämd frekvens.

-   **Uppdateringar av motorn för program mot skadlig kod**: uppdaterar automatiskt Microsoft Antimalware-motorn.

-   **Plattforms uppdateringar för program mot skadlig kod**: uppdaterar automatiskt plattformen Microsoft Antimalware.

-   **Aktivt skydd**: rapporterar telemetri metadata om identifierade hot och misstänkta resurser för att Microsoft Azure för att säkerställa snabba svar på det föränderliga hotet, liggande, vilket möjliggör överföring av synkron signatur i real tid via Microsoft Active Protection System.

-   **Exempel rapportering**: ger och rapporterar exempel till Microsoft Antimalware-tjänsten för att förfina tjänsten och aktivera fel sökning.

-   **Undantag**: gör att program-och tjänst administratörer kan konfigurera vissa filer, processer och enheter för undantag från skydd och genomsökning av prestanda och andra orsaker.

-   **Insamling av program mot skadlig kod**: registrerar hälso tillstånd för program mot skadlig kod, misstänkta aktiviteter och reparations åtgärder som vidtagits i händelse loggen för operativ systemet och samlar in dem i kundens Azure Storage-konto.

### <a name="azure-sql-database-threat-detection"></a>Azure SQL Database hot identifiering

[Azure SQL Database hot identifiering](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) är en ny säkerhetsintelligenss funktion som är inbyggd i Azure SQL Databases tjänsten. Om du arbetar dygnet runt att lära sig, profilera och identifiera avvikande databas aktiviteter, identifierar Azure SQL Database hot identifieringen potentiella hot mot databasen.

Säkerhets ansvariga eller andra angivna administratörer kan få omedelbara meddelanden om misstänkta databas aktiviteter när de inträffar. Varje meddelande ger information om den misstänkta aktiviteten och rekommenderar att du undersöker och minimerar risken ytterligare.

Azure SQL Database hot identifiering identifierar för närvarande potentiella sårbarheter och SQL-injektering och avvikande databas åtkomst mönster.

När användaren får ett hot identifierings meddelande kan användarna navigera och visa relevanta gransknings poster via en djup länk i e-postmeddelandet. Länken öppnar en gransknings visare eller en förkonfigurerad Excel-mall som visar relevanta gransknings poster runt tidpunkten för den misstänkta händelsen, enligt följande:

-   Granska lagring för databasen/servern med avvikande databas aktiviteter.

-   Relevant gransknings lagrings tabell som användes vid tidpunkten för händelsen att skriva gransknings loggen.

-   Gransknings poster för timmen omedelbart efter händelse förekomsten.

-   Granska poster med ett liknande händelse-ID vid tidpunkten för händelsen (valfritt för vissa identifieringar).

SQL Database hot detektorer använder någon av följande identifierings metoder:

-   **Deterministisk identifiering**: identifierar misstänkta mönster (regler baserade) i de SQL-klient frågor som matchar kända attacker. Den här metoden har hög identifiering och låg falsk positiv positiv, men begränsad täckning eftersom det ligger inom kategorin "atomiska identifieringar".

-   **Beteende identifiering**: identifierar avvikande aktivitet, vilket är onormalt beteende i databasen som inte setts under de senaste 30 dagarna. Exempel på avvikande aktivitet i SQL-klienten kan vara en insamling av misslyckade inloggningar eller frågor, en hög mängd data som extraheras, ovanliga kanoniska frågor eller okända IP-adresser som används för att komma åt databasen.

### <a name="application-gateway-web-application-firewall"></a>Application Gateway brand vägg för webbaserade program

[Brand vägg för webbaserade program (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) är en funktion i [Azure Application Gateway](../../web-application-firewall/ag/ag-overview.md) som skyddar webb program som använder en Programgateway för standard funktioner för [program leverans](https://kemptechnologies.com/in/application-delivery-controllers) . Brand väggen för webbaserade program gör detta genom att skydda dem mot de flesta [Öppna webb program säkerhets projekt (OWASP) med de 10 vanligaste webb sårbarheterna](https://owasp.org/www-project-top-ten/).

![Application Gateway brand Väggs diagram för webb program](./media/threat-detection/azure-threat-detection-fig13.png)

Skydd är:

-   Skydd för SQL-inmatning.

-   Skript skydd mellan platser.

-   Vanliga webb attacker, till exempel kommando inmatning, HTTP-begäran dold, delning av HTTP-svar och attack för att ta med fjärrfiler.

-   Skydd mot HTTP-protokollfel.

-   Skydd mot avvikelser i HTTP-protokollet, t. ex. saknade värd användar agenter och ta emot huvuden.

-   Skydd mot robotar, Crawler och skannrar.

-   Identifiering av vanliga felkonfigurationer för program (dvs. Apache, IIS och så vidare).

Att konfigurera WAF på din Application Gateway ger följande fördelar:

-   Skyddar ditt webb program från webb sårbarheter och attacker utan att ändra server dels koden.

-   Skyddar flera webb program samtidigt bakom en Programgateway. En Programgateway har stöd för att vara värd för upp till 20 webbplatser.

-   Övervakar webb program mot attacker genom att använda rapporter i real tid som genereras av WAF-loggar i Application Gateway.

-   Hjälper till att uppfylla kraven för efterlevnad. Vissa kontroll funktioner kräver att alla Internet-riktade slut punkter skyddas av en WAF-lösning.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>API för avvikelse identifiering: skapat med Azure Machine Learning

API: t för avvikelse identifiering är ett API som är användbart för att identifiera olika avvikande mönster i dina tids serie data. API: et tilldelar ett avvikande poäng till varje data punkt i tids serien, som kan användas för att generera aviseringar, övervaka via instrument paneler eller ansluta till dina biljett system.

[API: t för avvikelse identifiering](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) kan identifiera följande typer av avvikelser i tids serie data:

-   **Toppar och DIP**: när du övervakar antalet inloggnings försök till en tjänst eller antalet utcheckningar på en e-handelsplats kan ovanliga toppar eller dip tyda på säkerhets attacker eller tjänst avbrott.

-   **Positiva och negativa trender**: när du övervakar minnes användningen i data behandling innebär minskning av ledigt minne en potentiell minnes läcka. För övervakning av tjänstequeueens längd kan en kontinuerlig trend för att visa ett underliggande program varu problem uppstå.

-   **Nivå ändringar och ändringar i dynamiska värde intervall**: nivå ändringar i svars tid för en tjänst efter en tjänst uppgradering eller lägre nivåer av undantag efter uppgraderingen kan vara intressant att övervaka.

Det Machine Learning-baserade API: t möjliggör:

-   **Flexibel och robust identifiering**: med avvikelse identifierings modeller kan användarna konfigurera känslighets inställningar och identifiera avvikelser mellan säsongs-och icke-säsongs data uppsättningar. Användare kan justera avvikelse identifierings modellen för att göra identifierings-API: n mindre eller mer känslig enligt deras behov. Detta skulle innebära att identifiera mindre eller mer synliga avvikelser i data med och utan säsongs mönster.

-   **Skalbar och korrekt identifiering**: det traditionella sättet för övervakning med befintliga tröskelvärden som anges av experters domän kunskap är kostsamt och inte skalbart till miljon tals ändringar av data uppsättningar. De avvikande identifierings modellerna i detta API har lärts och modeller justeras automatiskt från både historiska och real tids data.

-   **Proaktivt och åtgärds bara identifiering**: långsam trend och nivå ändrings identifiering kan användas för tidig avvikelse identifiering. De tidigaste onormala signaler som identifieras kan användas för att dirigera människor till att undersöka och agera i problemområden. Dessutom kan rotor Saks analys modeller och aviserings verktyg utvecklas ovanpå denna API-tjänst för avvikelse identifiering.

API: t för avvikelse identifiering är en effektiv och effektiv lösning för en mängd olika scenarier, till exempel tjänste hälsa och KPI-övervakning, IoT, prestanda övervakning och övervakning av nätverks trafik. Här följer några populära scenarier där detta API kan vara användbart:

- IT-avdelningar behöver verktyg för att spåra händelser, fel kod, användnings logg och prestanda (CPU, minne och så vidare) i rimlig tid.

-   Online Commerce-webbplatser vill spåra kund aktiviteter, sid visningar, klick och så vidare.

-   Verktyg som företag vill spåra förbrukning av vatten, gas, elektricitet och andra resurser.

-   Anläggningar eller bygg hanterings tjänster vill övervaka temperatur, fukt, trafik och så vidare.

-   IoT/Manufacturers vill använda sensor data i tids serier för att övervaka arbets flöde, kvalitet och så vidare.

-   Tjänst leverantörer, till exempel Call Center, behöver övervaka service demand trend, incident volym, wait Kölängd och så vidare.

-   Företags analys grupper vill övervaka företags-KPI: er (till exempel försäljnings volym, kund sentiment eller prissättning) onormal rörelse i real tid.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](/cloud-app-security/what-is-cloud-app-security) är en viktig del av Microsoft Cloud säkerhets stacken. Det är en omfattande lösning som kan hjälpa din organisation när du flyttar för att dra full nytta av moln programs löfte. Det ger dig kontroll, genom förbättrad insyn i aktiviteten. Det hjälper också till att bättre skydda viktiga data i molnappar.

Med verktyg för att upptäcka Shadow IT, utvärdera risker, genomdriva principer, undersöka aktiviteter och stoppa hot kan organisationen migrera till molnet på ett säkrare sätt samtidigt som ni behåller kontrollen över viktiga data.

| Kategori | Beskrivning |
| -------- | ----------- |
| Identifiera | Upptäck Shadow IT med Cloud App Security. Få insyn genom att identifiera appar, aktiviteter, användare, data och filer i molnmiljön. Identifiera tredjepartsappar som är anslutna till molnet.|
|Undersök
 | Undersök molnapparna med hjälp av datautredningsverktyg för att analysera riskfyllda appar, specifika användare och filer i nätverket. Hitta mönster i de data som samlas in från molnet. Generera rapporter för att övervaka molnet. |
| Kontroll | Minska riskerna genom att skapa principer och varningar som ger dig fullständig kontroll över molntrafiken i nätverket. Använd Cloud App Security för att migrera användarna till säkra sanktionerade molnappar. |
| Skydda | Använd Cloud App Security för att sanktionera eller förhindra program, framtvinga skydd mot data förlust, kontrol lera behörigheter och delning och skapa anpassade rapporter och aviseringar. |
| Kontroll | Minska riskerna genom att skapa principer och varningar som ger dig fullständig kontroll över molntrafiken i nätverket. Använd Cloud App Security för att migrera användarna till säkra sanktionerade molnappar. |


![Cloud App Security diagram](./media/threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integrerar synligheten med ditt moln genom att:

-   Använda Cloud Discovery för att mappa och identifiera din moln miljö och de molnappar som din organisation använder.

-   Sanktionera och förhindra appar i molnet.

-   Använda lättanvända app-kopplingar som utnyttjar Provider-API: er, för synlighet och styrning av appar som du ansluter till.

-   Att hjälpa dig att få kontinuerlig kontroll genom att ställa in och sedan kontinuerligt finjustera principer.

Vid insamling av data från dessa källor, Cloud App Security köra avancerad analys på den. Du varnas omedelbart om avvikande aktiviteter och får djupgående insyn i molnmiljön. Du kan konfigurera en princip i Cloud App Security och använda den för att skydda allt innehåll i molnmiljön.

## <a name="third-party-threat-protection-capabilities-through-the-azure-marketplace"></a>Hot skydds funktioner från tredje part via Azure Marketplace

### <a name="web-application-firewall"></a>Brandvägg för webbaserade program

Brand väggen för webbaserade program kontrollerar inkommande webb trafik och blockerar SQL-injektioner, skript körning över flera webbplatser, överföringar av skadlig kod, program DDoS attacker och andra attacker riktade mot dina webb program. Det kontrollerar också svaren från backend-webbservrar för data förlust skydd (DLP). Med den integrerade åtkomst kontroll motorn kan administratörer skapa detaljerade principer för åtkomst kontroll för autentisering, auktorisering och redovisning (AAA), vilket ger organisationer stark autentisering och användar kontroll.

Brand väggen för webbaserade program ger följande fördelar:

-   Identifierar och blockerar SQL-injektioner, skript körning över flera webbplatser, överföring av skadlig kod, program DDoS eller andra attacker mot ditt program.

-   Autentisering och åtkomst kontroll.

-   Söker igenom utgående trafik för att identifiera känsliga data och kan maskera eller blockera information från att läcka ut.

-   Påskyndar leveransen av webb program innehåll med funktioner som cachelagring, komprimering och annan trafik optimering.

Exempel på brand väggar för webb program som är tillgängliga på Azure Marketplace finns i [Barracuda WAF, Brocade Virtual Web Application Firewall (vWAF), Imperva SecureSphere och ThreatSTOP IP-brandvägg](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Nästa steg

- [Svara på dagens hot](../../security-center/security-center-managing-and-responding-alerts.md): hjälper till att identifiera aktiva hot som riktar sig mot dina Azure-resurser och ger de insikter du behöver för att svara snabbt.

- [Azure SQL Database hot identifiering](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): hjälper dig att hantera dina problem om potentiella hot mot dina databaser.