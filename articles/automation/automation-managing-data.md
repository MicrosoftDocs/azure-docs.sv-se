---
title: Azure Automation data säkerhet
description: Den här artikeln hjälper dig att lära dig hur Azure Automation skyddar din integritet och skyddar dina data.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 2bdf25ef24f1fbf4aaf4dec154ea6af3421b915a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050825"
---
# <a name="management-of-azure-automation-data"></a>Hantera Azure Automation-data

Den här artikeln innehåller flera ämnen som förklarar hur data skyddas och skyddas i en Azure Automations miljö.

## <a name="tls-12-enforcement-for-azure-automation"></a>TLS 1,2-tillämpning för Azure Automation

För att säkerställa säkerheten för data som överförs till Azure Automation rekommenderar vi starkt att du konfigurerar användningen av Transport Layer Security (TLS) 1,2. Följande är en lista över metoder eller klienter som kommunicerar via HTTPS till Automation-tjänsten:

* Webhook-anrop

* Hybrid Runbook Worker, som innehåller datorer som hanteras av Uppdateringshantering och Ändringsspårning och inventering.

* DSC-noder

Äldre versioner av TLS/Secure Sockets Layer (SSL) har befunnits vara sårbara och även om de fortfarande arbetar för att tillåta bakåtkompatibilitet, rekommenderas de **inte**. Vi rekommenderar att du inte uttryckligen anger att agenten ska använda TLS 1,2 om det inte är absolut nödvändigt, eftersom det kan bryta säkerhets funktioner på plattforms nivå som gör att du automatiskt kan identifiera och dra nytta av nyare säkra protokoll när de blir tillgängliga, t. ex. TLS 1,3.

Information om stöd för TLS 1,2 med Log Analytics-agenten för Windows och Linux, vilket är ett beroende för Hybrid Runbook Worker-rollen finns i [Log Analytics agent översikt – TLS 1,2](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol).

### <a name="platform-specific-guidance"></a>Plattformsspecifik vägledning

|Plattform/språk | Support | Mer information |
| --- | --- | --- |
|Linux | Linux-distributioner tenderar att förlita sig på [openssl](https://www.openssl.org) för TLS 1,2-stöd.  | Kontrol lera [openssl-ändringsloggen](https://www.openssl.org/news/changelog.html) för att bekräfta att din version av OpenSSL stöds.|
| Windows 8,0-10 | Stöds och är aktiverat som standard. | För att bekräfta att du fortfarande använder [standardinställningarna](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012-2016 | Stöds och är aktiverat som standard. | Bekräfta att du fortfarande använder [standardinställningarna](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 och Windows Server 2008 R2 SP1 | Stöds, men är inte aktiverat som standard. | På sidan [Transport Layer Security (TLS) register inställningar](/windows-server/security/tls/tls-registry-settings) finns mer information om hur du aktiverar.  |

## <a name="data-retention"></a>Datakvarhållning

När du tar bort en resurs i Azure Automation sparas den i ett antal dagar för gransknings syfte innan permanent borttagning. Du kan inte se eller använda resursen under den här tiden. Den här principen gäller även för resurser som tillhör ett borttaget Automation-konto. Bevarande principen gäller för alla användare och kan för närvarande inte anpassas. Men om du behöver lagra data under en längre period kan du [vidarebefordra Azure Automation jobb data till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md).

I följande tabell sammanfattas bevarande principen för olika resurser.

| Data | Policy |
|:--- |:--- |
| Konton |Ett konto tas bort permanent 30 dagar efter att användaren tagit bort det. |
| Tillgångar |En till gång tas bort permanent 30 dagar efter att användaren tagit bort den, eller 30 dagar efter att en användare har tagit bort ett konto som innehåller till gången. Till gångar innehåller variabler, scheman, autentiseringsuppgifter, certifikat, python 2-paket och anslutningar. |
| DSC-noder |En DSC-nod tas bort permanent 30 dagar efter att ha avregistrerats från ett Automation-konto med hjälp av Azure Portal eller [unregister-AzAutomationDscNode-](/powershell/module/az.automation/unregister-azautomationdscnode) cmdleten i Windows PowerShell. En nod tas också bort permanent 30 dagar efter att användaren tagit bort det konto som innehåller noden. |
| Jobb |Ett jobb tas bort och tas bort permanent 30 dagar efter ändringar, till exempel när jobbet har slutförts, stoppats eller har pausats. |
| Moduler |En modul tas bort permanent 30 dagar efter att användaren tagit bort den eller 30 dagar efter att användaren tagit bort det konto som innehåller modulen. |
| Nodkonfigurationer/MOF-filer |En gammal Node-konfiguration tas bort permanent 30 dagar efter att en ny nod har skapats. |
| Node-rapporter |En Node-rapport tas bort permanent 90 dagar efter att en ny rapport har genererats för noden. |
| Runbooks |En Runbook tas bort permanent 30 dagar efter att en användare tagit bort resursen, eller 30 dagar efter att användaren tagit bort kontot som innehåller resursen<sup>1</sup>. |

<sup>1</sup> Runbooken kan återställas inom 30-dagars period genom att skicka in en support incident för Azure med Microsoft Azure support. Gå till [Support webbplatsen för Azure](/support/options) och välj **skicka en support förfrågan**.

## <a name="data-backup"></a>Säkerhets kopiering av data

När du tar bort ett Automation-konto i Azure raderas alla objekt i kontot. Objekten inkluderar Runbooks, moduler, konfigurationer, inställningar, jobb och till gångar. De kan inte återställas när kontot har tagits bort. Du kan använda följande information för att säkerhetskopiera innehållet i ditt Automation-konto innan du tar bort det.

### <a name="runbooks"></a>Runbooks

Du kan exportera dina runbooks till skriptfiler med antingen Azure Portal-eller [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) -cmdleten i Windows PowerShell. Du kan importera dessa skriptfiler till ett annat Automation-konto, enligt beskrivningen i [Hantera Runbooks i Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Integreringsmoduler

Du kan inte exportera integrerings moduler från Azure Automation. Du måste göra dem tillgängliga utanför Automation-kontot.

### <a name="assets"></a>Tillgångar

Du kan inte exportera Azure Automation till gångar: certifikat, anslutningar, autentiseringsuppgifter, scheman och variabler. I stället kan du använda Azure Portal och Azure-cmdletar för att anteckna information om dessa till gångar. Använd sedan informationen för att skapa alla till gångar som används av Runbooks som du importerar till ett annat Automation-konto.

Du kan inte hämta värdena för krypterade variabler eller lösen ords fält för autentiseringsuppgifter med hjälp av cmdletar. Om du inte känner till dessa värden kan du hämta dem i en Runbook. Information om hur du hämtar variabel värden finns [i variabel till gångar i Azure Automation](shared-resources/variables.md). Om du vill veta mer om hur du hämtar autentiseringsuppgifter, se [inloggnings till gångar i Azure Automation](shared-resources/credentials.md).

### <a name="dsc-configurations"></a>DSC-konfigurationer

Du kan exportera DSC-konfigurationerna till skriptfiler med antingen Azure Portal eller cmdleten [export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) i Windows PowerShell. Du kan importera och använda dessa konfigurationer i ett annat Automation-konto.

## <a name="geo-replication-in-azure-automation"></a>Geo-replikering i Azure Automation

Geo-replikering är standard i Azure Automation-konton. Du väljer en primär region när du konfigurerar ditt konto. Den interna Automation-tjänsten för geo-replikering tilldelar kontot automatiskt en sekundär region. Tjänsten säkerhetskopierar sedan kontinuerligt konto data från den primära regionen till den sekundära regionen. Den fullständiga listan med primära och sekundära regioner finns i [verksamhets kontinuitet och haveri beredskap (BCDR): Azure-kopplade regioner](../best-practices-availability-paired-regions.md).

Säkerhets kopian som skapats av tjänsten Automation geo-Replication är en fullständig kopia av Automation-tillgångar, konfigurationer och liknande. Den här säkerhets kopian kan användas om den primära regionen slutar fungera och förlorar data. Om det osannoliks att data för en primär region förloras försöker Microsoft återställa det. Om företaget inte kan återställa primära data använder den automatisk redundans och informerar dig om situationen genom din Azure-prenumeration.

Tjänsten Automation geo-Replication är inte tillgänglig direkt för externa kunder om det uppstår ett regionalt haveri. Om du vill underhålla automatiserings konfiguration och Runbooks under regionala haverier:

1. Välj en sekundär region som du vill koppla till det geografiska området för ditt primära Automation-konto.

2. Skapa ett Automation-konto i den sekundära regionen.

3. Exportera dina runbooks som skriptfiler i det primära kontot.

4. Importera runbooks till ditt Automation-konto i den sekundära regionen.

## <a name="next-steps"></a>Nästa steg

* Mer information om säkra till gångar i Azure Automation finns i [kryptering av säkra till gångar i Azure Automation](automation-secure-asset-encryption.md).

* Mer information om geo-replikering finns i [skapa och använda aktiv geo-replikering](../azure-sql/database/active-geo-replication-overview.md).
