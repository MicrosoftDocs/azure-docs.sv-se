---
title: Nyheter i Azure Sentinel
description: I den här artikeln beskrivs nya funktioner i Azure Sentinel från de senaste månaderna.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 9136947767bffb7bea800cdd2a735794baf8f329
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007371"
---
# <a name="whats-new-in-azure-sentinel"></a>Nyheter i Azure Sentinel

Den här artikeln innehåller de senaste funktionerna som har lagts till för Azure Sentinel och nya funktioner i relaterade tjänster som ger en bättre användar upplevelse i Azure Sentinel.

Information om tidigare funktioner som levereras finns i våra [Tech community-Bloggar](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Antecknade funktioner finns för närvarande i för hands version. I [tilläggs villkoren för Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ingår ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte har publicerats i allmän tillgänglighet.


> [!TIP]
> Våra team för hot jakt i Microsoft Contribute-frågor, spel böcker, arbets böcker och antecknings böcker till [Azure Sentinel-communityn](https://github.com/Azure/Azure-Sentinel), inklusive vissa [frågor](https://github.com/Azure/Azure-Sentinel) som dina team kan anpassa och använda. 
>
> Du kan också delta! Delta i [Azure Sentinel Threat Hunters GitHub-communityn](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="january-2021"></a>Januari 2021

- [Analys regel guiden: förbättrad redigerings upplevelse för frågor (offentlig för hands version)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [PowerShell-modulen AZ. SecurityInsights (offentlig för hands version)](#azsecurityinsights-powershell-module-public-preview)
- [SQL Database-anslutning](#sql-database-connector)
- [Förbättrade incident kommentarer](#improved-incident-comments)
- [Dedikerade Log Analytics kluster](#dedicated-log-analytics-clusters)
- [Hanterade identiteter för Logic Apps](#logic-apps-managed-identities)
- [Förbättrad regel justering med för hands versions diagram för analys regel](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Analys regel guiden: förbättrad redigerings upplevelse för frågor (offentlig för hands version)

Guiden regel för schemalagd analys i Azure Sentinel innehåller nu följande förbättringar för att skriva och redigera frågor:

-   Ett expanderbart redigerings fönster som ger dig mer skärm utrymme för att visa din fråga.
-   Ord markering för ord i din frågeparameter.
-   Utökat stöd för automatisk komplettering.
-   Valideringar av frågor i real tid. Fel i frågan visas nu som ett rött block i rullnings listen och som en röd prick på fliken **Ange regelns logiska** namn. Dessutom går det inte att spara en fråga med fel.

Mer information finns i [Självstudier: identifiera hot direkt från rutan](tutorial-detect-threats-built-in.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>PowerShell-modulen AZ. SecurityInsights (offentlig för hands version)

Azure Sentinel stöder nu den nya [AZ. SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) PowerShell-modulen.

Modulen **AZ. SecurityInsights** har stöd för vanliga scenarier för Azure Sentinel-användning, som att interagera med incidenter för att ändra Statues, allvarlighets grad, ägare och så vidare, lägga till kommentarer och etiketter till incidenter och skapa bok märken.

Även om vi rekommenderar att du använder [Azure Resource Manager-mallar (arm)](/azure/azure-resource-manager/templates/) för din CI/CD-pipeline, är modulen **AZ. SecurityInsights** användbar för åtgärder efter distributionen och är avsedd för SOC Automation.  Till exempel kan SOC Automation innehålla steg för att konfigurera data kopplingar, skapa analys regler eller lägga till automatiserings åtgärder i analys regler.

Mer information, inklusive en fullständig lista och beskrivning av tillgängliga cmdlets, parameter beskrivningar och exempel finns i PowerShell-dokumentationen för [AZ. SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>SQL Database-anslutning

Azure Sentinel tillhandahåller nu en Azure SQL Database-anslutning, som du kan använda för att strömma dina databasers gransknings-och diagnostikloggar till Azure Sentinel och kontinuerligt övervaka aktivitet i alla dina instanser.

Azure SQL är en fullständigt hanterad databas motor för PaaS (Platform-as-a-Service) som hanterar de flesta funktioner för databas hantering, till exempel uppgradering, uppdatering, säkerhets kopiering och övervakning, utan medverkan från användaren.

Mer information finns i [ansluta Azure SQL Database-diagnostik och gransknings loggar](connect-azure-sql-logs.md).

### <a name="improved-incident-comments"></a>Förbättrade incident kommentarer

Analytiker använder incident kommentarer för att samar beta om incidenter, dokumentera processer och steg manuellt eller som en del av en Spelbok. 

Med vår förbättrade händelse för incident Kommentering kan du formatera dina kommentarer och redigera eller ta bort befintliga kommentarer.

Mer information finns i [skapa incidenter automatiskt från Microsofts säkerhets aviseringar](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Dedikerade Log Analytics kluster

Azure Sentinel stöder nu dedikerade Log Analytics kluster som ett distributions alternativ. Vi rekommenderar att du överväger ett dedikerat kluster om du:

- Mata in **över 1 TB per dag** i Azure Sentinel-arbetsytan
- **Ha flera Azure Sentinel-arbetsytor** i din Azure-registrering

Med dedikerade kluster kan du använda funktioner som Kundhanterade nycklar, säker databas, dubbel kryptering och snabbare frågor över flera arbets ytor när du har flera arbets ytor i samma kluster.

Mer information finns i [Azure Monitor loggar dedicerade kluster](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters).

### <a name="logic-apps-managed-identities"></a>Hanterade identiteter för Logic Apps

Azure Sentinel stöder nu hanterade identiteter för Azure Sentinel Logic Apps-anslutningen, så att du kan bevilja behörigheter direkt till en speciell Spelbok för att arbeta med Azure Sentinel i stället för att skapa extra identiteter.

- **Utan en hanterad identitet** kräver Logic Apps-anslutningen en separat identitet med en Azure Sentinel RBAC-roll för att kunna köras på Azure Sentinel. Den separata identiteten kan vara en Azure AD-användare eller ett huvud namn för tjänsten, till exempel ett registrerat Azure AD-program.

- Att **Aktivera stöd för hanterad identitet i din Logic app** registrerar Logic-appen med Azure AD och ger ett objekt-ID. Använd objekt-ID i Azure Sentinel för att tilldela Logic-appen en Azure RBAC-roll i din Azure Sentinel-arbetsyta. 

Mer information finns i:

- [Autentisering med hanterad identitet i Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
- [Dokumentation om Azure Sentinel Logic Apps Connector](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Förbättrad regel justering med analys regel för hands versions diagram (offentlig för hands version)

Azure Sentinel hjälper dig nu att bättre finjustera analys reglerna, vilket hjälper dig att öka precisionen och minska bruset.

När du har redigerat en analys regel på fliken **Ange regel logik** hittar du avsnittet **resultat simulering** till höger. 

Välj **testa med aktuella data** om du vill att Azure Sentinel ska köra en simulering av de senaste 50 körningarna av din analys regel. Ett diagram skapas för att visa det genomsnittliga antalet aviseringar som regeln skulle ha genererat, baserat på rå data som utvärderats. 

Mer information finns i [definiera regel frågans logik och konfigurera inställningar](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>December 2020

- [80 nya inbyggda jakt frågor](#80-new-built-in-hunting-queries)
- [Förbättringar av Log Analytics-agenten](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nya inbyggda jakt frågor
 
Azure Sentinels inbyggda jakt frågor ger SOC-analytiker möjlighet att minska luckor i nuvarande detektions täckning och tända nya jakt ledare.

Den här uppdateringen för Azure Sentinel innehåller nya jakt frågor som ger täckning över MITRE to&CK Framework-matris:

- **Samling**
- **Kommando och kontroll**
- **Åtkomst till autentiseringsuppgifter**
- **Identifiering**
- **Projektering**
- **Exfiltrering**
- **Påverkan**
- **Initial åtkomst**
- **Ständig**
- **Eskalering av privilegier**

De extra jakt frågorna är utformade för att hjälpa dig att hitta misstänkt aktivitet i din miljö. Även om de kan returnera legitim aktivitet och potentiellt skadlig aktivitet kan de vara användbara för att ge din jakt. 

Om du när du har kört dessa frågor kan du känna dig trygg med resultaten, du kanske vill konvertera dem till analys regler eller lägga till jakt resultat till befintliga eller nya incidenter.

Alla tillagda frågor är tillgängliga via sidan Azure Sentinel jakt. Mer information finns i [jakt efter hot med Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Förbättringar av Log Analytics-agenten

Azure Sentinel-användare drar nytta av följande förbättringar i Log Analytics-agenten:

- **Stöd för fler operativ system**, inklusive CentOS 8, RedHat 8 och SUSE Linux 15.
- **Stöd för python 3** förutom python 2

Azure Sentinel använder Log Analytics agent för att skicka händelser till din arbets yta, inklusive Windows säkerhets händelser, Syslog-händelser, CEF-loggar med mera.

> [!NOTE]
> Den Log Analytics agenten kallas ibland OMS-agenten eller Microsoft Monitoring Agent (MMA). 
> 

Mer information finns i Log Analytics- [dokumentationen](/azure/azure-monitor/platform/log-analytics-agent) och viktig information om [Log Analytics agent](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>November 2020

- [Övervaka din Logic Apps spel böcker i Azure Sentinel](#monitor-your-logic-apps-playbooks-in-azure-sentinel)
- [Microsoft 365 Defender-koppling (offentlig för hands version)](#microsoft-365-defender-connector-public-preview)
### <a name="monitor-your-logic-apps-playbooks-in-azure-sentinel"></a>Övervaka din Logic Apps spel böcker i Azure Sentinel

Azure Sentinel integreras nu med [Azure log Apps](/azure/logic-apps/), en moln tjänst som hjälper dig att schemalägga, automatisera och dirigera uppgifter, affärs processer och arbets flöden.

Använd en Azure Logic-app i Azure Sentinel som en Spelbok, som kan anropas automatiskt när en incident skapas, eller när sorterar och arbetar med incidenter. 

För att ge insikter om hälso tillståndet, prestandan och användningen av din spel böcker, inklusive de som du lägger till med Azure Logic Apps, har vi lagt till en [Azure-arbetsbok](/azure/azure-monitor/platform/workbooks-overview) med namnet **spel böcker Health Monitoring**. 

Använd arbets boken **spel böcker Health Monitoring** för att övervaka hälso tillståndet för din spel böcker eller leta efter avvikelser i mängden lyckade eller misslyckade körningar. 

Arbets boken **spel böcker Health Monitoring** är nu tillgänglig i galleriet för Azure Sentinel-mallar:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Exempel arbets bok för spel böcker Health Monitoring":::

Mer information finns i:

- [Logic Apps dokumentation](/azure/logic-apps/monitor-logic-apps-log-analytics#set-up-azure-monitor-logs)

- [Dokumentation för Azure Monitor](/azure/azure-monitor/platform/activity-log#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender-koppling (offentlig för hands version)
 
Med Microsoft 365 Defender-anslutaren för Azure Sentinel kan du strömma avancerade jakt loggar (en typ av rå händelse data) från Microsoft 365 Defender till Azure Sentinel. 

Med integreringen av [Microsoft Defender för slut punkt (MDATP)](/windows/security/threat-protection/) i säkerhets paraplyen för [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) kan du nu samla in dina Microsoft Defender för slut punkts avancerade jakt händelser med hjälp av Microsoft 365 Defender-anslutningen och strömma dem direkt i nya syfte-skapade tabeller i Azure Sentinel-arbetsytan. 

Azure Sentinel-tabellerna bygger på samma schema som används i Microsoft 365 Defender-portalen, och ger dig fullständig åtkomst till en fullständig uppsättning avancerade jakt loggar. 

Mer information finns i [ansluta data från Microsoft 365 Defender till Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender kallades tidigare Microsoft Threat Protection eller MTP. Microsoft Defender för slut punkten kallades tidigare Microsoft Defender Advanced Threat Protection eller MDATP.
> 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Fordonsbaserad Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Få insyn i aviseringar](quickstart-get-visibility.md)
