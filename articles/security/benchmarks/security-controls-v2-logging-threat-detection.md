---
title: Azure Security benchmark v2 – loggning och hot identifiering
description: Azure-säkerhet för benchmark v2-loggning och hot identifiering
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: a274ec1aab3f530700f89ef810fa667fdf4d08e6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042988"
---
# <a name="security-control-v2-logging-and-threat-detection"></a>Säkerhets kontroll v2: loggning och hot identifiering

Loggning och hot identifiering omfattar kontroller för att identifiera hot på Azure och aktivera, samla in och lagra gransknings loggar för Azure-tjänster. Detta inkluderar aktivering av identifierings-, undersöknings-och reparations processer med kontroller för att generera aviseringar med hög kvalitet med inbyggd hot identifiering i Azure-tjänster. den omfattar också insamling av loggar med Azure Monitor, centraliserad säkerhets analys med Azure Sentinel, tidssynkronisering och logg kvarhållning.

Om du vill se vilka inbyggda Azure Policy som finns kan du läsa [mer i information om det inbyggda initiativet för Azure Security benchmark-efterlevnad: loggning och hot identifiering](../../governance/policy/samples/azure-security-benchmark.md#logging-and-threat-detection)

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivera hot identifiering för Azure-resurser

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| LT-1 | 6.7 | AU-3, AU-6, AU-12, SI-4 |

Se till att du övervakar olika typer av Azure-tillgångar för potentiella hot och avvikelser. Fokusera på att få aviseringar med hög kvalitet för att minska antalet falska positiva identifieringar för analytiker som ska sorteras. Aviseringar kan hämtas från loggdata, agenter eller andra data.

Använd Azure Security Center inbyggd hot identifierings funktion, som baseras på övervakning av Azure-tjänsten telemetri och analys av tjänst loggar. Data samlas in med hjälp av Log Analytics agent, som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från systemet och kopierar data till din arbets yta för analys. 

Använd dessutom Azure Sentinel för att bygga analys regler, som rör hot som matchar specifika kriterier i din miljö. Reglerna genererar incidenter när villkoren matchas, så att du kan undersöka varje incident. Azure Sentinel kan också importera Hot information från tredje part för att förbättra sin funktion för hot identifiering. 

- [Skydd mot hot i Azure Security Center](../../security-center/azure-defender.md)

- [Referens guide för Azure Security Center säkerhets varningar](../../security-center/alerts-reference.md)

- [Skapa anpassade analys regler för att identifiera hot](../../sentinel/tutorial-detect-threats-custom.md)

- [Cyberhot Hot information med Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Säkerhetsåtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Hot information](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hotidentifiering i hanteringen av identiteter och åtkomst i Azure

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| LT-2 | 6.8 | AU-3, AU-6, AU-12, SI-4 |

Azure AD innehåller följande användar loggar som kan visas i Azure AD repor ting eller integreras med Azure Monitor, Azure Sentinel eller andra SIEM/övervaknings verktyg för mer avancerade övervaknings-och analys användnings fall: 
-   Inloggningsaktiviteter – Rapporten för inloggningsaktiviteter ger information om användningen av hanterade program och användares inloggningsaktiviteter.

-   Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.

-   Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

-   Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Azure Security Center kan även Varna vid vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök och föråldrade konton i prenumerationen. Utöver den grundläggande övervakningen av säkerhetsövervakning kan Azure Security Centerens skydds modul även samla in mer djupgående säkerhets aviseringar från enskilda Azure Compute-resurser (till exempel virtuella datorer, behållare, App Service), data resurser (till exempel SQL DB och lagring) och Azure Service-lager. Med den här funktionen kan du Visa konto avvikelser i de enskilda resurserna.

- [Granska aktivitets rapporter i Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Aktivera Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Skydd mot hot i Azure Security Center](../../security-center/azure-defender.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Säkerhetsåtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Hot information](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning av nätverksaktiviteter i Azure

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| LT-3 | 9,3, 12,2, 12,5, 12,8 | AU-3, AU-6, AU-12, SI-4 |

Aktivera och samla in resurs loggar för nätverks säkerhets grupper (NSG), NSG Flow-loggar, Azure Firewall-loggar och WAF-loggar (Web Application Firewall) för säkerhets analys för att stödja incident undersökningar, Hot jakt och generering av säkerhets aviseringar. Du kan skicka flödes loggarna till en Azure Monitor Log Analytics arbets yta och sedan använda Trafikanalys för att ge insikter.

Se till att du samlar in DNS-frågegrupper för att hjälpa till att korrelera andra nätverks data.

- [Så här aktiverar du flödes loggar för nätverks säkerhets grupper](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Firewall-loggar och mått](../../firewall/logs-and-metrics.md)

- [Så här aktiverar och använder du Trafikanalys](../../network-watcher/traffic-analytics.md)

- [Övervakning med Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)

- [Lösningar för övervakning av Azure-nätverk i Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md)

- [Samla in insikter om din DNS-infrastruktur med DNS-analys-lösningen](../../azure-monitor/insights/dns-analytics.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Säkerhetsåtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Hot information](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| LT-4 | 6,2, 6,3, 8,8 | AU-3, AU-12 |

Aktivera loggning för Azure-resurser för att uppfylla kraven för efterlevnad, Hot identifiering, jakt och incident undersökning. 

Du kan använda Azure Security Center och Azure Policy för att aktivera resurs loggar och logga data insamling på Azure-resurser för åtkomst till gransknings-, säkerhets-och resurs loggar. Aktivitets loggar, som är automatiskt tillgängliga, innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element.

- [Förstå loggning och olika logg typer i Azure](../../azure-monitor/essentials/platform-logs-overview.md)

- [Förstå Azure Security Center insamling av data](../../security-center/security-center-enable-data-collection.md)

**Ansvar**: Delad

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsåtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

Infrastruktur- och slutpunktssäkerhet 

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Hot information](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Central hantering och analys av säkerhetsloggar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| LT-5 | 6,5, 6,6 | AU-3, SI-4 |

Centralisera loggning av lagring och analys för att aktivera korrelation. För varje logg källa ser du till att du har tilldelat en data ägare, åtkomst vägledning, lagrings plats, vilka verktyg som används för att bearbeta och komma åt data samt krav för data lagring.

Se till att du integrerar Azures aktivitets loggar i den centrala loggningen. Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Du kan också aktivera och publicera data till Azure Sentinel eller en SIEM från tredje part.

Många organisationer väljer att använda Azure Sentinel för "heta" data som används ofta och Azure Storage för "kall" data som används mindre ofta.

- [Samla in plattforms loggar och mått med Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av loggar

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| LT-6 | 6.4 | AU-3, AU-11 |

Konfigurera logg kvarhållning enligt dina krav på efterlevnad, reglering och affärs verksamhet. 

I Azure Monitor kan du Log Analytics ange arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage-, Data Lake-eller Log Analytics arbets ytans konton för långsiktig lagring och arkivering.

- [Ändra data lagrings perioden i Log Analytics](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurera bevarande princip för Azure Storage konto loggar](../../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [Azure Security Center aviseringar och rekommendationer exportera](../../security-center/continuous-export.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Säkerhetsåtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Hantering av säkerhetsefterlevnad](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: Använd godkända tids källor för synkronisering

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| LT-7 | 6.1 | AU-8 |

Microsoft hanterar tids källor för de flesta Azure-PaaS och SaaS-tjänster. För dina virtuella datorer använder du Microsoft standard NTP-server för tidssynkronisering om du inte har ett speciellt krav. Om du behöver skapa en egen NTP-server (Network Time Protocol) ser du till att port 123 för UDP-tjänsten skyddas.

Alla loggar som genereras av resurser i Azure tillhandahåller tidsstämplar med tids zonen som anges som standard.

- [Så här konfigurerar du tidssynkronisering för Azure Windows Compute-resurser](../../virtual-machines/windows/time-sync.md)

- [Så här konfigurerar du tidssynkronisering för Azure Linux-beräknings resurser](../../virtual-machines/linux/time-sync.md)

- [Så här inaktiverar du inkommande UDP för Azure-tjänster](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**Ansvar**: Delad

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Principer och standarder](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)