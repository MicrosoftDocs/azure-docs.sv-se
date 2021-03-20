---
title: Planera rapporter & övervakning av distribution – Azure AD
description: Beskriver hur du planerar och kör implementering av rapportering och övervakning.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 683367251c220abe36660d61463bce9e5a0c52f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577769"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Planera en Azure Active Directory rapportering och övervakning av distribution

Din Azure Active Directory (Azure AD)-rapportering och övervaknings lösning är beroende av dina juridiska, säkerhets-och drifts krav och din befintliga miljö och befintliga processer. Den här artikeln visar de olika design alternativen och vägleder dig till rätt distributions strategi.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Fördelar med Azure AD-rapportering och-övervakning

Azure AD repor ting innehåller en omfattande vy och loggar av Azure AD-aktivitet i din miljö, inklusive inloggnings händelser, gransknings händelser och ändringar i din katalog.

Med dessa data kan du:

* Bestäm hur dina appar och tjänster används.

* identifiera potentiella risker som påverkar hälso tillståndet för din miljö.

* Felsök problem som hindrar användarna från att få jobbet gjort.

* få insikter genom att se gransknings händelser av ändringar i Azure AD-katalogen.

> [!IMPORTANT]
> Med Azure AD-övervakning kan du dirigera loggar som genereras av Azure AD repor ting till olika mål system. Du kan antingen behålla dem för långvarig användning eller integrera dem med SIEM-verktyg (säkerhetsinformation och händelsehantering) för att få insikter om din miljö.

Med Azure AD-övervakning kan du dirigera loggar till:

* ett Azure Storage-konto för arkivering.
* Azure Monitor loggar, som tidigare kallades Azure Log Analytics-arbetsytan, där du kan analysera data, skapa instrument paneler och avisering om vissa händelser.
* en Azure-händelsehubben där du kan integrera med dina befintliga SIEM-verktyg som Splunk, SumoLogic eller QRadar.

> [!NOTE]
Vi började nyligen använda term Azure Monitors loggar i stället för Log Analytics. Loggdata lagras fortfarande i en Log Analytics arbets yta och samlas in och analyseras fortfarande av samma Log Analytics-tjänst. Vi uppdaterar terminologin för att bättre avspegla rollen för [loggar i Azure Monitor](../../azure-monitor/data-platform.md). Se [Azure Monitor terminologis ändringar](../../azure-monitor/terminology.md) för mer information.

[Läs mer om rapport bevarande principer](./reference-reports-data-retention.md).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licensiering och krav för Azure AD-rapportering och-övervakning

Du behöver en Azure AD Premium-licens för att få åtkomst till Azure AD-inloggnings loggarna.

Detaljerad information om funktioner och licensiering i [Azure Active Directory prissättnings guide](https://azure.microsoft.com/pricing/details/active-directory/).

För att distribuera Azure AD-övervakning och-rapportering behöver du en användare som är global administratör eller säkerhets administratör för Azure AD-klienten.

Beroende på den slutliga destinationen för dina loggdata behöver du något av följande:

* Ett Azure-lagringskonto som du har ListKeys-behörigheter för. Vi rekommenderar att du använder ett allmänt lagringskonto och inte ett blob-lagringskonto. Information om lagringspriser hittar du i [Priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Ett Azure Event Hubs-namnområde som kan integreras med SIEM-lösningar från tredje part.

* En Azure Log Analytics-arbetsyta för att skicka loggar till Azure Monitor loggar.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Planera ett distributions projekt för Azure repor ting och övervakning

I det här projektet definierar du de mål grupper som ska förbruka och övervaka rapporter och definiera din Azure AD-övervaknings arkitektur.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt inte fungerar, gör de vanligt vis på grund av felaktiga förväntningar på påverkan, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du är engagerande rätt intressenter](../fundamentals/active-directory-deployment-plans.md). Se också till att från intressenter roller i projektet är väl förstå genom att dokumentera intressenterna och deras ingångs-och Accountabilities.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Kommunicera proaktivt med dina användare hur deras upplevelse kommer att ändras, när den kommer att ändras och hur du får support om de drabbas av problem.

### <a name="document-your-current-infrastructure-and-policies"></a>Dokumentera din aktuella infrastruktur och dina principer

Din aktuella infrastruktur och dina befintliga principer kommer att driva din design för rapportering och övervakning. Se till att du vet

* Vad, om några, SIEM verktyg du använder.

* Din Azure-infrastruktur, inklusive befintliga lagrings konton och övervakning som används.

* Organisationens bevarande principer för loggar, inklusive eventuella tillämpliga ramverk för efterlevnad som krävs. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Planera en distribution av Azure AD-rapportering och övervakning

Rapportering och övervakning används för att uppfylla dina affärs behov, få insikter om användnings mönster och öka din organisations säkerhets position.

### <a name="business-use-cases"></a>Användnings fall för företag

* Krävs för att lösningen ska uppfylla affärs behoven
* Bra att uppfylla affärs behoven
* Inte tillämpligt

|Område |Beskrivning |
|-|-|
|Kvarhållning| **Logg kvarhållning på mer än 30 dagar**. På grund av juridiska krav eller affärs krav krävs det att lagra gransknings loggar och logga in loggar för Azure AD längre än 30 dagar. |
|Analys| **Loggarna måste vara** sökbara. De lagrade loggarna måste vara sökbara med analys verktyg. |
| Operational Insights| **Insikter för olika team**. Behovet av att ge åtkomst till olika användare för att få Operational Insights, till exempel program användning, inloggnings fel, självbetjänings användning, trender osv. |
| Säkerhets insikter| **Insikter för olika team**. Behovet av att ge åtkomst till olika användare för att få Operational Insights, till exempel program användning, inloggnings fel, självbetjänings användning, trender osv. |
| Integrering i SIEM-system      | **Siem-integrering**. Behovet av att integrera och strömma Azure AD-inloggnings loggar och gransknings loggar till befintliga SIEM-system. |

### <a name="choose-a-monitoring-solution-architecture"></a>Välj en arkitektur för övervaknings lösning

Med Azure AD-övervakning kan du dirigera dina Azure AD-aktivitets loggar till ett system som bäst uppfyller dina affärs behov. Du kan sedan spara dem för långsiktig rapportering och analys för att få insikter om din miljö och integrera den med SIEM-verktyg.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Diagram över besluts flöde![En bild som visar vad som beskrivs i följande avsnitt](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Arkivera loggar i ett lagrings konto

Genom att routa loggar till ett Azure Storage-konto kan du behålla dem längre än den standard kvarhållningsperiod som beskrivs i våra [bevarande principer](./reference-reports-data-retention.md). Använd den här metoden om du behöver arkivera loggarna, men inte behöver integrera dem med ett SIEM-system och inte behöver pågående frågor och analyser. Du kan fortfarande göra sökningar på begäran.

Lär dig hur du [dirigerar data till ditt lagringskonto](./quickstart-azure-monitor-route-logs-to-storage-account.md).

#### <a name="send-logs-to-azure-monitor-logs"></a>Skicka loggar till Azure Monitor-loggar

[Azure Monitor loggar](../../azure-monitor/logs/log-query-overview.md) konsoliderar övervaknings data från olika källor. Det innehåller också ett frågespråk och analys motor som ger dig insikter om hur dina program och resurser används. Genom att skicka Azure AD-aktivitets loggar till Azure Monitor loggar kan du snabbt hämta, övervaka och varna på insamlade data. Använd den här metoden om du inte har en befintlig SIEM-lösning som du vill skicka dina data till direkt, men vill ha frågor och analyser. När dina data finns i Azure Monitor loggar kan du skicka dem till händelsehubben och därifrån till en SIEM om du vill.

Lär dig att [skicka data till Azure Monitor-loggar](./howto-integrate-activity-logs-with-log-analytics.md).

Du kan också installera förinställda vyer för Azure AD-aktivitets loggar för att övervaka vanliga scenarier som innefattar inloggnings-och gransknings händelser.

Lär dig att [installera och använda logganalysvyer för Azure AD-aktivitetsloggar](./howto-install-use-log-analytics-views.md).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Strömma loggar till din Azure Event Hub

Routning loggar till en Azure Event Hub möjliggör integrering med SIEM-verktyg från tredje part. Den här integrationen gör att du kan kombinera Azure AD-aktivitetsloggdata med andra data som hanteras av ditt SIEM, för att ge bättre inblick i din miljö. 

Lär dig hur du [skickar händelser till en händelsehubb](./tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Planera åtgärder och säkerhet för Azure AD-rapportering och-övervakning

Intressenter behöver åtkomst till Azure AD-loggar för att få Operational Insights. Troliga användare inkluderar säkerhets grupps medlemmar, interna eller externa revisorer och teamet för hantering av identitets-och åtkomst hantering.

Med Azure AD-roller kan du delegera möjligheten att konfigurera och Visa Azure AD-rapporter baserat på din roll. Identifiera vem i din organisation som behöver behörighet att läsa Azure AD-rapporter och vilken roll som skulle vara lämplig för dem. 

Följande roller kan läsa Azure AD-rapporter:

* Global administratör

* Säkerhetsadministratör

* Säkerhetsläsare

* Rapportläsare

Lär dig mer om [administrativa roller i Azure AD](../roles/permissions-reference.md).

*Använd alltid begreppet minsta behörighet för att minska risken för att ett konto komprometteras*. Överväg att implementera [Privileged Identity Management](../privileged-identity-management/pim-configure.md) för att skydda din organisation ytterligare.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Distribuera Azure AD-rapportering och-övervakning

Beroende på de beslut som du har gjort tidigare med hjälp av design anvisningarna ovan kommer det här avsnittet att vägleda dig till dokumentationen om de olika distributions alternativen.

### <a name="consume-and-archive-azure-ad-logs"></a>Använda och arkivera Azure AD-loggar

[Hitta aktivitetsrapporter på Azure-portalen](./howto-find-activity-reports.md)

[Arkivera Azure AD-loggar till ett Azure Storage konto](./quickstart-azure-monitor-route-logs-to-storage-account.md)

### <a name="implement-monitoring-and-analytics"></a>Implementera övervakning och analys

[Skicka loggar till Azure Monitor](./howto-integrate-activity-logs-with-log-analytics.md)

[Installera och Använd Log Analytics-vyerna för Azure Active Directory](./howto-install-use-log-analytics-views.md)

[Analysera Azure AD-aktivitets loggar med Azure Monitor loggar](./howto-analyze-activity-logs-log-analytics.md)

* [Tolka schemat för gransknings loggar i Azure Monitor](./reference-azure-monitor-audit-log-schema.md)

* [Tolka schema för inloggnings loggar i Azure Monitor](./reference-azure-monitor-sign-ins-log-schema.md)

 * [Strömma Azure AD-loggar till en Azure Event Hub](./tutorial-azure-monitor-stream-logs-to-event-hub.md)

* [Integrera Azure AD-loggar med Splunk med hjälp av Azure Monitor](./howto-integrate-activity-logs-with-splunk.md)

* [Integrera Azure AD-loggar med SumoLogic med hjälp av Azure Monitor](./howto-integrate-activity-logs-with-sumologic.md)

 

 

## <a name="next-steps"></a>Nästa steg

Överväg att implementera [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 

Överväg att implementera [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md)