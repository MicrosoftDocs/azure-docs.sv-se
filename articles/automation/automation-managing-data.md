---
title: Azure Automation datasäkerhet
description: Den här artikeln beskriver hur Azure Automation skyddar din integritet och dina data.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb18cca782b85e608c3c7ddb001ecb03b86055f6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833534"
---
# <a name="management-of-azure-automation-data"></a>Hantera Azure Automation-data

Den här artikeln innehåller flera ämnen som förklarar hur data skyddas och skyddas i en Azure Automation miljö.

## <a name="tls-12-enforcement-for-azure-automation"></a>Tvingande TLS 1.2 för Azure Automation

För att säkerställa säkerheten för data som överförs till Azure Automation rekommenderar vi starkt att du konfigurerar användningen av Transport Layer Security (TLS) 1.2. Följande är en lista över metoder eller klienter som kommunicerar via HTTPS till Automation-tjänsten:

* Webhook-anrop

* Hybrid Runbook Workers, som omfattar datorer som hanteras av Uppdateringshantering och Ändringsspårning och inventering.

* DSC-noder

Äldre versioner av TLS/Secure Sockets Layer (SSL) har visat sig vara sårbara och även om de fortfarande fungerar för att tillåta bakåtkompatibilitet rekommenderas **de inte**. Vi rekommenderar inte att du uttryckligen anger att agenten endast ska använda TLS 1.2 om det inte behövs, eftersom det kan bryta säkerhetsfunktioner på plattformsnivå som gör att du automatiskt kan identifiera och dra nytta av nyare säkrare protokoll när de blir tillgängliga, till exempel TLS 1.3.

Information om TLS 1.2-stöd med Log Analytics-agenten för Windows och Linux, som är ett beroende för Hybrid Runbook Worker-rollen, finns i [Översikt över Log Analytics-agenten – TLS 1.2.](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol)

### <a name="platform-specific-guidance"></a>Plattformsspecifik vägledning

|Plattform/språk | Support | Mer information |
| --- | --- | --- |
|Linux | Linux-distributioner förlitar [sig ofta på OpenSSL](https://www.openssl.org) för stöd för TLS 1.2.  | Kontrollera [OpenSSL-ändringsloggen](https://www.openssl.org/news/changelog.html) för att bekräfta att din version av OpenSSL stöds.|
| Windows 8.0–10 | Stöds och aktiveras som standard. | Bekräfta att du fortfarande använder [standardinställningarna](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 – 2016 | Stöds och aktiveras som standard. | Bekräfta att du fortfarande använder [standardinställningarna](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 och Windows Server 2008 R2 SP1 | Stöds, men är inte aktiverat som standard. | Se sidan [Transport Layer Security registerinställningar (TLS) för](/windows-server/security/tls/tls-registry-settings) mer information om hur du aktiverar.  |

## <a name="data-retention"></a>Datakvarhållning

När du tar bort en Azure Automation sparas den i många dagar i granskningssyfte innan den tas bort permanent. Du kan inte se eller använda resursen under den här tiden. Den här principen gäller även för resurser som tillhör ett borttagna Automation-konto. Bevarandeprincipen gäller för alla användare och kan för närvarande inte anpassas. Men om du behöver behålla data under en längre period kan du vidarebefordra Azure Automation [jobbdata till Azure Monitor loggar .](automation-manage-send-joblogs-log-analytics.md)

I följande tabell sammanfattas bevarandeprincipen för olika resurser.

| Data | Policy |
|:--- |:--- |
| Konton |Ett konto tas bort permanent 30 dagar efter att användaren har tagit bort det. |
| Tillgångar |En tillgång tas bort permanent 30 dagar efter att en användare tar bort den, eller 30 dagar efter att en användare tar bort ett konto som innehåller tillgången. Tillgångar omfattar variabler, scheman, autentiseringsuppgifter, certifikat, Python 2-paket och anslutningar. |
| DSC-noder |En DSC-nod tas bort permanent 30 dagar efter att den avregistrerats från ett Automation-konto med hjälp av Azure Portal eller cmdleten [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) i Windows PowerShell. En nod tas också bort permanent 30 dagar efter att en användare tar bort det konto som innehåller noden. |
| Jobb |Ett jobb tas bort och tas bort permanent 30 dagar efter ändringen, till exempel när jobbet har slutförts, stoppas eller pausas. |
| Moduler |En modul tas bort permanent 30 dagar efter att en användare tar bort den, eller 30 dagar efter att en användare tar bort det konto som innehåller modulen. |
| Nodkonfigurationer/MOF-filer |En gammal nodkonfiguration tas bort permanent 30 dagar efter att en ny nodkonfiguration har genererats. |
| Node-rapporter |En nodrapport tas bort permanent 90 dagar efter att en ny rapport har genererats för noden. |
| Runbooks |En runbook tas bort permanent 30 dagar efter att en användare tar bort resursen, eller 30 dagar efter att en användare tar bort kontot som innehåller resursen<sup>1</sup>. |

<sup>1</sup> Runbooken kan återställas inom 30-dagarsfönstret genom att du lämnar in ett Azure-supportincident Microsoft Azure support. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Skicka en supportbegäran.**

## <a name="data-backup"></a>Säkerhetskopiering av data

När du tar bort ett Automation-konto i Azure tas alla objekt i kontot bort. Objekten omfattar runbooks, moduler, konfigurationer, inställningar, jobb och tillgångar. De kan inte återställas när kontot har tagits bort. Du kan använda följande information för att backa upp innehållet i ditt Automation-konto innan du tar bort det.

### <a name="runbooks"></a>Runbooks

Du kan exportera dina runbooks till skriptfiler med antingen Azure Portal eller cmdleten [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) i Windows PowerShell. Du kan importera dessa skriptfiler till ett annat Automation-konto, enligt vad som beskrivs i [Hantera runbooks i Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Integreringsmoduler

Du kan inte exportera integreringsmoduler från Azure Automation, de måste göras tillgängliga utanför Automation-kontot.

### <a name="assets"></a>Tillgångar

Du kan inte exportera Azure Automation: certifikat, anslutningar, autentiseringsuppgifter, scheman och variabler. I stället kan du använda Azure Portal och Azure-cmdlets för att notera informationen om dessa tillgångar. Använd sedan den här informationen för att skapa tillgångar som används av runbooks som du importerar till ett annat Automation-konto.

Du kan inte hämta värdena för krypterade variabler eller lösenordsfälten för autentiseringsuppgifter med hjälp av cmdlets. Om du inte känner till dessa värden kan du hämta dem i en runbook. Information om hur du hämtar variabelvärden finns [i Variabeltillgångar i Azure Automation](shared-resources/variables.md). Mer information om hur du hämtar autentiseringsvärden finns i [Autentiseringstillgångar i Azure Automation](shared-resources/credentials.md).

### <a name="dsc-configurations"></a>DSC-konfigurationer

Du kan exportera dina DSC-konfigurationer till skriptfiler med hjälp av antingen Azure Portal eller cmdleten [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) i Windows PowerShell. Du kan importera och använda dessa konfigurationer i ett annat Automation-konto.

## <a name="geo-replication-in-azure-automation"></a>Geo-replikering i Azure Automation

Geo-replikering är standard i Azure Automation konton. Du väljer en primär region när du ställer in ditt konto. Den interna automationstjänsten för geo-replikering tilldelar automatiskt en sekundär region till kontot. Tjänsten backar sedan kontinuerligt kontodata från den primära regionen till den sekundära regionen. Den fullständiga listan över primära och sekundära regioner finns i Affärskontinui och haveriberedskap [(BCDR): Azure-parkopplade regioner](../best-practices-availability-paired-regions.md).

Säkerhetskopian som skapats av automationstjänsten för georeplikering är en fullständig kopia av Automation-tillgångar, konfigurationer och liknande. Den här säkerhetskopian kan användas om den primära regionen ligger nere och förlorar data. I de osannolika fall då data för en primär region går förlorade försöker Microsoft återställa dem.

> [!NOTE]
> Azure Automation lagrar kunddata i den region som valts av kunden. För BCDR lagras data för alla regioner utom Brasilien, södra och Sydostasien, Azure Automation i en annan region (länkad Azure-region). Endast för regionen Brasilien, södra (Delstaten Sao Paulo) i Brasiliens geografiska område och Sydostasien-region (Singapore) för Asien och stillahavsområdet-geografin, lagrar vi Azure Automation-data i samma region för att tillgodose kraven på datahemhemlighet för dessa regioner.

Automations tjänst för geo-replikering är inte tillgänglig direkt för externa kunder om det uppstår ett regionalt fel. Om du vill underhålla Automation-konfiguration och runbooks vid regionala fel:

1. Välj en sekundär region som ska parkopplas med det geografiska området för ditt primära Automation-konto.

2. Skapa ett Automation-konto i den sekundära regionen.

3. Exportera dina runbooks som skriptfiler i det primära kontot.

4. Importera runbooks till ditt Automation-konto i den sekundära regionen.

## <a name="next-steps"></a>Nästa steg

* Mer information om säkra tillgångar i Azure Automation finns i [Kryptering av säkra tillgångar i Azure Automation](automation-secure-asset-encryption.md).

* Mer information om geo-replikering finns i Skapa [och använda aktiv geo-replikering.](../azure-sql/database/active-geo-replication-overview.md)
