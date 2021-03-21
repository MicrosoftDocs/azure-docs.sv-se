---
title: Översikt över Azures drift säkerhet | Microsoft Docs
description: Lär dig mer om Azures drift säkerhet i den här översikten. Drift säkerhet avser till gångs skydds tjänster,-kontroller och-funktioner.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: 4bc30fbf342a9bc85b52c9f88ce7ca1df3c36e23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595496"
---
# <a name="azure-operational-security-overview"></a>Översikt över Azures drift säkerhet

[Azures drift säkerhet](./operational-security.md) syftar på tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda data, program och andra till gångar i Microsoft Azure. Det är ett ramverk som införlivar den kunskap som du har fått via en rad olika funktioner som är unika för Microsoft. Dessa funktioner omfattar Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-programmet och djupgående medvetenhet om cybersäkerhet Threat landskap.

## <a name="azure-management-services"></a>Hanterings tjänster för Azure

En IT-avdelning ansvarar för att hantera data Center infrastruktur, program och data, inklusive stabilitet och säkerhet för dessa system. Att få till gång till säkerhets insikter i ökande komplexa IT-miljöer kräver dock ofta att organisationer Cobble samman data från flera säkerhets-och hanterings system.

[Microsoft Azure övervaka loggar](../../azure-monitor/overview.md) är en molnbaserad IT-hanterings lösning som hjälper dig att hantera och skydda din lokala infrastruktur och moln infrastruktur. De viktigaste funktionerna tillhandahålls av följande tjänster som körs i Azure. Azure innehåller flera tjänster som hjälper dig att hantera och skydda din lokala infrastruktur och moln infrastruktur. Varje tjänst tillhandahåller en speciell hanterings funktion. Du kan kombinera tjänster för att uppnå olika hanterings scenarier. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../../azure-monitor/overview.md) samlar in data från hanterade källor till centrala data lager. Dessa data kan omfatta händelser, prestanda data eller anpassade data som tillhandahålls via API: et. När data har samlats in är de tillgängliga för aviseringar, analyser och export.

Du kan konsolidera data från en mängd olika källor och kombinera data från dina Azure-tjänster med din befintliga lokala miljö. Azure Monitor loggar avgränsar också tydligt data mängden från den åtgärd som vidtas för dessa data, så att alla åtgärder är tillgängliga för alla typer av data.

### <a name="automation"></a>Automation

[Azure Automation](../../automation/automation-intro.md) ger dig möjlighet att automatisera de manuella, långvariga, fel känsliga och ofta återkommande uppgifter som utförs ofta i en moln-och företags miljö. Det sparar tid och ökar tillförlitligheten för administrativa uppgifter. Det kan även schemalägga att aktiviteterna ska utföras automatiskt med jämna mellanrum. Du kan automatisera processer med hjälp av Runbooks eller automatisera konfigurations hantering genom att använda önskad tillstånds konfiguration.

### <a name="backup"></a>Backup

[Azure Backup](../../backup/backup-overview.md) är den Azure-baserade tjänsten som du kan använda för att säkerhetskopiera (eller skydda) och återställa data i Microsoft Cloud. Azure Backup ersätter din befintliga säkerhets kopierings lösning lokalt eller från en annan plats med en molnbaserad lösning som är tillförlitlig, säker och kostnads konkurrens kraftig.

Azure Backup innehåller komponenter som du hämtar och distribuerar på lämplig dator eller server, eller i molnet. Komponenten eller agenten som du distribuerar beror på vad du vill skydda. Alla Azure Backup-komponenter (oavsett om du skyddar data lokalt eller i molnet) kan användas för att säkerhetskopiera data till ett Azure Recovery Services-valv i Azure.

Mer information finns i [tabellen Azure Backup-komponenter](../../backup/backup-overview.md#what-can-i-back-up).

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) ger affärs kontinuitet genom att dirigera replikeringen av lokala virtuella och fysiska datorer till Azure, eller till en sekundär plats. Om den primära platsen inte är tillgänglig växlar du över till den sekundära platsen så att användarna kan fortsätta att arbeta. Du växlar tillbaka när systemen återgår till arbets ordningen. Använd Azure Security Center för att utföra mer intelligent och effektiv hot identifiering.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](../../active-directory/manage-apps/what-is-application-management.md) är en omfattande identitets tjänst som:

-   Aktiverar identitets-och åtkomst hantering (IAM) som en moln tjänst.
-   Tillhandahåller central åtkomst hantering, enkel inloggning (SSO) och rapportering.
-   Stöder integrerad åtkomst hantering för [tusentals program](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) på Azure Marketplace, inklusive Salesforce, Google Apps, Box och Concur.

Azure AD innehåller också en fullständig uppsättning [funktioner för identitets hantering](./identity-management-overview.md#security-monitoring-alerts-and-machine-learning-based-reports), inklusive följande:

- [Multifaktorautentisering](../../active-directory/authentication/concept-mfa-howitworks.md)
- [Lösenordshantering via självbetjäning](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Grupp hantering via självbetjäning](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
- [Hantering av privilegierade konton](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure RBAC (rollbaserad åtkomstkontroll)](../../role-based-access-control/overview.md)
- [Övervakning av program användning](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Omfattande granskning](../../active-directory/reports-monitoring/concept-audit-logs.md)
- [Säkerhetsövervakning och aviseringar](../../security-center/security-center-managing-and-responding-alerts.md)

Med Azure Active Directory har alla program som du publicerar för dina partner och kunder (företag eller konsumenter) samma funktioner för identitets-och åtkomst hantering. På så sätt kan du avsevärt minska dina drifts kostnader.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) hjälper dig att förhindra, identifiera och svara på hot med ökad insyn i (och kontroll över) säkerheten för dina Azure-resurser. Den ger integrerad säkerhetsövervakning och princip hantering i dina prenumerationer. Den hjälper till att upptäcka hot som annars kan gå vidare och fungerar med ett brett eko system med säkerhetslösningar.

[Skydda data för virtuella datorer](../../security-center/security-center-introduction.md) i Azure genom att tillhandahålla insyn i den virtuella datorns säkerhets inställningar och övervakning av hot. Security Center kan övervaka dina virtuella datorer för:

- Säkerhets inställningar för operativ systemet med de rekommenderade konfigurations reglerna.
- Systemsäkerhet och viktiga uppdateringar som saknas.
- Rekommendationer för Endpoint Protection.
- Verifiering av disk kryptering.
- Nätverksbaserade attacker.

Security Center använder [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md). Azure RBAC innehåller [inbyggda roller](../../role-based-access-control/built-in-roles.md) som kan tilldelas användare, grupper och tjänster i Azure.

Security Center bedömer konfigurationen av dina resurser för att identifiera säkerhets problem och sårbarheter. I Security Center visas endast information som rör en resurs när du har tilldelats rollen som ägare, deltagare eller läsare för den prenumeration eller resurs grupp som en resurs tillhör.

>[!Note]
>Mer information om roller och tillåtna åtgärder i Security Center finns i [behörigheter i Azure Security Center](../../security-center/security-center-permissions.md).

Security Center använder Microsoft Monitoring Agent. Detta är samma agent som den Azure Monitor tjänsten använder. Data som samlas in från den här agenten lagras antingen i en befintlig Log Analytics [arbets yta](../../azure-monitor/logs/manage-access.md) som är kopplad till din Azure-prenumeration eller en ny arbets yta, med hänsyn till den virtuella datorns plats.

## <a name="azure-monitor"></a>Azure Monitor

Prestanda problem i Cloud-appen kan påverka din verksamhet. Med flera sammankopplade komponenter och frekventa versioner kan degraderingen ske när som helst. Och om du utvecklar en app kan användarna vanligt vis upptäcka problem som du inte har hittat under testningen. Du bör känna till problemen direkt och du bör ha verktyg för att diagnostisera och åtgärda problemen.

[Azure Monitor](../../azure-monitor/overview.md) är ett grundläggande verktyg för övervakning av tjänster som körs på Azure. Det ger dig data på infrastruktur nivå om data flödet för en tjänst och omgivande miljö. Om du hanterar dina appar i Azure och bestämmer om du vill skala upp eller ned resurser, Azure Monitor är den plats där du vill starta.

Du kan också använda övervaknings data för att få djupgående insikter om ditt program. Den kunskapen kan hjälpa dig att förbättra programmets prestanda eller hanterbarhet, eller automatisera åtgärder som annars skulle kräva manuell åtgärd.

Azure Monitor innehåller följande komponenter.

### <a name="azure-activity-log"></a>Azure-aktivitetslogg

[Azure aktivitets loggen](../../azure-monitor/essentials/platform-logs-overview.md) ger inblick i de åtgärder som utfördes på resurser i din prenumeration. Det kallades tidigare "Gransknings logg" eller "drift logg", eftersom den rapporterar kontroll Plans händelser för dina prenumerationer.

### <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar

[Azure Diagnostic-loggar](../../azure-monitor/essentials/platform-logs-overview.md) genereras av en resurs och ger omfattande, frekventa data om driften av resursen. Innehållet i dessa loggar varierar beroende på resurs typ.

Windows Event System-loggar är en kategori av diagnostikloggar för virtuella datorer. BLOB-, tabell-och Queue-loggar är kategorier av diagnostikloggar för lagrings konton.

Diagnostikloggar skiljer sig från [aktivitets loggen](../../azure-monitor/essentials/platform-logs-overview.md). Aktivitets loggen ger inblick i de åtgärder som utfördes på resurser i din prenumeration. Diagnostikloggar ger insikter om åtgärder som din resurs utfört själv.

### <a name="metrics"></a>Mått

Azure Monitor tillhandahåller telemetri som ger dig insyn i prestanda och hälsa för dina arbets belastningar i Azure. Den viktigaste typen av Azure-telemetridata är [måtten](../../azure-monitor/data-platform.md) (kallas även prestanda räknare) som har genererats av de flesta Azure-resurser. Azure Monitor tillhandahåller flera olika sätt att konfigurera och använda dessa mått för övervakning och fel sökning.

### <a name="azure-diagnostics"></a>Microsoft Azure Diagnostics

Azure-diagnostik aktiverar insamling av diagnostikdata i ett distribuerat program. Du kan använda diagnostik-tillägget från olika källor. För närvarande finns stöd för [Azure Cloud Service-roller](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure virtuella datorer](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service) som kör Microsoft Windows och [Azure Service Fabric](../../azure-monitor/agents/diagnostics-extension-overview.md).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Kunderna skapar ett nätverk från slut punkt till slut punkt i Azure genom att dirigera och skriva enskilda nätverks resurser som virtuella nätverk, Azure ExpressRoute, Azure Application Gateway och belastningsutjämnare. Övervakning är tillgängligt på varje nätverks resurs.

Slutpunkt-till-slutpunkt-nätverket kan ha komplexa konfigurationer och interaktioner mellan resurser. Resultatet är komplexa scenarier som kräver scenario-baserad övervakning via [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

Network Watcher fören klar övervakning och diagnostisering av ditt Azure-nätverk. Du kan använda verktyg för diagnostik och visualisering i Network Watcher för att:

- Ta fjärrpakets insamling på en virtuell Azure-dator.
- Få insikter om din nätverks trafik genom att använda flödes loggar.
- Diagnostisera Azure-VPN Gateway och anslutningar.

Network Watcher har för närvarande följande funktioner:

- [Topologi](../../network-watcher/view-network-topology.md): innehåller en översikt över de olika samman kopplingarna och associationerna mellan nätverks resurser i en resurs grupp.
- [Variabel paket fångst](../../network-watcher/network-watcher-packet-capture-overview.md): fångar in paket data i och ut ur en virtuell dator. Avancerade alternativ för filtrering och finjusterade kontroller, till exempel möjligheten att ange tid- och storleksbegränsningar, ger flexibilitet. Paket data kan lagras i ett BLOB-lager eller på den lokala disken i. Cap-format.
- [Verifiera IP-flöde](../../network-watcher/network-watcher-ip-flow-verify-overview.md): kontrollerar om ett paket tillåts eller nekas baserat på paket parametrar med 5 tupel för flödes information (mål-IP, käll-IP, målport, källport och protokoll). Om en säkerhets grupp nekar paketet returneras den regel och grupp som nekade paketet.
- [Nästa hopp](../../network-watcher/network-watcher-next-hop-overview.md): anger nästa hopp för paket som dirigeras i Azures nätverks infrastruktur resurs, så att du kan diagnostisera eventuella felkonfigurerade användardefinierade vägar.
- [Vyn säkerhets grupp](../../network-watcher/network-watcher-security-group-view-overview.md): hämtar de effektiva och tillämpade säkerhets reglerna som tillämpas på en virtuell dator.
- [NSG flödes loggar för nätverks säkerhets grupper](../../network-watcher/network-watcher-nsg-flow-logging-overview.md): gör att du kan avbilda loggar som är relaterade till trafik som tillåts eller nekas av säkerhets reglerna i gruppen. Flödet definieras med 5-tuple-information: käll-IP, mål-IP, källport, mål Port och protokoll.
- [Virtuell nätverksgateway och anslutnings fel sökning](../../network-watcher/network-watcher-troubleshoot-manage-rest.md): ger möjlighet att felsöka virtuella nätverks-gatewayer och anslutningar.
- [Begränsningar för nätverks prenumeration](../../network-watcher/network-watcher-monitoring-overview.md): gör att du kan visa användning av nätverks resurser mot gränser.
- [Diagnostikloggar](../../network-watcher/network-watcher-monitoring-overview.md): tillhandahåller ett enda fönster för att aktivera eller inaktivera diagnostikloggar för nätverks resurser i en resurs grupp.

Mer information finns i [konfigurera Network Watcher](../../network-watcher/network-watcher-create.md).

## <a name="cloud-service-provider-access-transparency"></a>Moln tjänst leverantörens åtkomst genomskinlighet

[Customer lockbox för Microsoft Azure](customer-lockbox-overview.md) är en tjänst som är integrerad i Azure Portal och som ger dig explicit kontroll i den sällsynta instansen när en Microsoft support-tekniker kan behöva åtkomst till dina data för att lösa ett problem.
Det finns väldigt få instanser, till exempel en fel sökning av problem med fjärråtkomst, där en Microsoft Support-tekniker kräver förhöjd behörighet för att lösa det här problemet. I sådana fall använder Microsoft-tekniker just-in-Time-tjänsten som tillhandahåller begränsad tidsbegränsad auktorisering med begränsad till gång till tjänsten.  
Även om Microsoft redan har fått kund medgivande för åtkomst, kan Customer Lockbox nu ge dig möjlighet att granska och godkänna eller avvisa sådana förfrågningar från Azure-portalen. Microsofts support tekniker kommer inte att beviljas åtkomst förrän du godkänner begäran.

## <a name="standardized-and-compliant-deployments"></a>Standardiserade och kompatibla distributioner

[Azure-ritningar](../../governance/blueprints/overview.md) aktiverar Cloud Architects och Central informations teknik grupper för att definiera en upprepnings bar uppsättning Azure-resurser som implementerar och följer en organisations standarder, mönster och krav.  
Detta gör det möjligt för DevOps-team att snabbt bygga och sätta igång nya miljöer och lita på att de bygger dem med infrastruktur som upprätthåller organisationens efterlevnad.
Ritningar ger ett deklarativ sätt att dirigera distributionen av olika resursfiler och andra artefakter som:

- Rolltilldelningar
- Principtilldelningar
- Azure Resource Manager-mallar
- Resursgrupper

## <a name="devops"></a>DevOps

Innan [DevOps-program (Developer Operations)](https://azure.microsoft.com/overview/what-is-devops/) är det ansvarigt att samla in affärs krav för ett program och skriva kod. Sedan har ett separat frågor och svar testat programmet i en isolerad utvecklings miljö. Om kraven uppfylldes, utgjorde frågor och svar-teamet koden för att distribuera. Distributions teamen fragmenterades ytterligare i grupper som nätverk och databas. Varje gången ett program utlöstes via väggen till ett oberoende team lades Flask halsar till.

DevOps gör det möjligt för team att leverera säkrare, högre kvalitets lösningar snabbare och mer billigt. Kunderna förväntar sig en dynamisk och tillförlitlig upplevelse vid användning av program vara och tjänster. Teamen måste snabbt iterera efter program uppdateringar och mäta effekten av uppdateringarna. De måste svara snabbt med nya utvecklings iterationer för att åtgärda problem eller tillhandahålla mer värde.  

Moln plattformarna som Microsoft Azure har tagit bort traditionella Flask halsar och hjälpte commoditize-infrastruktur. Program vara REIGNS i alla verksamheter som nyckel differentiering och faktor i affärs resultat. Ingen organisation, utvecklare eller IT-anställd kan eller bör undvika DevOps rörelse.

DevOps praktiserande läkare antar flera av följande metoder. Dessa metoder [omfattar personer](/azure/devops/learn/what-is-devops-culture) för att forma strategier baserat på affärs scenarier. Verktyg kan automatisera de olika metoderna.

- [Flexibla planerings-och projekt hanterings](https://www.visualstudio.com/learn/what-is-agile/) tekniker används för att planera och isolera arbete i Sprint, hantera teamets kapacitet och hjälpa teamen att snabbt anpassa sig till föränderliga affärs behov.
- [Versions kontroll, vanligt vis med git](/azure/devops/learn/git/what-is-git), gör att team som finns var som helst i världen kan dela källan och integrera med program utvecklings verktyg för att automatisera lanserings pipelinen.
- [Kontinuerlig integrering](/azure/devops/learn/what-is-continuous-integration) styr pågående sammanslagning och testning av kod, vilket leder till att hitta fel tidigt.  Andra förmåner omfattar mindre tid på att bekämpa sammanfognings problem och snabbt feedback för utvecklings team.
- [Kontinuerlig leverans](/azure/devops/learn/what-is-continuous-delivery) av program varu lösningar till produktions-och test miljöer hjälper organisationer att snabbt åtgärda buggar och svara på föränderliga affärs behov.
- [Övervakning](/azure/devops/learn/what-is-monitoring) av program som körs, inklusive produktions miljöer för program hälsa, samt kund användning – hjälper organisationer att bilda en hypotes och snabbt validera eller Visa strategier.  Omfattande data samlas in och lagras i olika loggnings format.
- [Infrastruktur som kod (IaC)](/azure/devops/learn/what-is-infrastructure-as-code) är en metod som möjliggör automatisering och validering av skapande och Teardown av nätverk och virtuella datorer för att hjälpa till med att leverera säkra, stabila program värd plattformar.
- Arkitektur för [mikrotjänster](/azure/devops/learn/what-are-microservices) används för att isolera företags användnings fall till små återanvändbara tjänster.  Den här arkitekturen möjliggör skalbarhet och effektivitet.

## <a name="next-steps"></a>Nästa steg

Mer information om Säkerhet och granskning-lösningen finns i följande artiklar:

- [Säkerhet och efterlevnad](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](../../security-center/security-center-introduction.md)
- [Azure Monitor](../../azure-monitor/overview.md)