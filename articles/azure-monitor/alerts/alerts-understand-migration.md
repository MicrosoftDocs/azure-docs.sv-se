---
title: Förstå migrering för Azure Monitor aviseringar
description: Lär dig hur migreringen av aviseringar fungerar och Felsök problem.
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: fdac8015cf87ffa0a25a8558668329a8cd82327f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737196"
---
# <a name="understand-migration-options-to-newer-alerts"></a>Förstå migrerings alternativ för nya aviseringar

Klassiska aviseringar [dras tillbaka](./monitoring-classic-retirement.md) för offentliga moln användare, men fortfarande i begränsad användning till och med **31 maj 2021**. Klassiska aviseringar för Azure Government molnet och Azure Kina 21Vianet kommer att dra tillbaka den **29 februari 2024**.

I den här artikeln förklaras hur det manuella migrerings-och volontär verktyget fungerar, som kommer att användas för att migrera återstående aviserings regler. Det beskriver också lösningar för några vanliga problem.

> [!IMPORTANT]
> Aktivitets logg aviseringar (inklusive tjänste hälso aviseringar) och logg aviseringar påverkas inte av migreringen. Migreringen gäller endast klassiska varnings regler som beskrivs [här](./monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

> [!NOTE]
> Om de klassiska varnings reglerna är ogiltiga, dvs. de finns i [inaktuella mått](#classic-alert-rules-on-deprecated-metrics) eller resurser som har tagits bort, kommer de inte att migreras och kommer inte att vara tillgängliga när tjänsten har dragits tillbaka.

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>Migrera klassiska varningar manuellt till nyare aviseringar

Kunder som är intresserade av att manuellt migrera sina återstående aviseringar kan redan göra detta med hjälp av följande avsnitt. Den innehåller också mått som har dragits tillbaka och därför inte kan migreras direkt.

### <a name="guest-metrics-on-virtual-machines"></a>Gäst mått på virtuella datorer

Innan du kan skapa nya mått aviseringar för gäst mått måste gäst måtten skickas till Azure Monitor loggar Store. Följ de här anvisningarna för att skapa aviseringar:

- [Aktivera insamling av gäst mått till Log Analytics](../agents/agent-data-sources.md)
- [Skapa logg aviseringar i Azure Monitor](./alerts-log.md)

Det finns fler alternativ för att samla in gäst mått och aviseringar på dem, mer [information](../agents/agents-overview.md).

### <a name="storage-and-classic-storage-account-metrics"></a>Lagrings konto mått för lagring och klassisk lagring

Alla klassiska aviseringar för lagrings konton kan migreras utom aviseringar för dessa mått:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

Klassiska varnings regler för procent mått måste migreras baserat på [mappningen mellan gamla och nya lagrings mått](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics). Tröskelvärden måste ändras på lämpligt sätt eftersom det nya måttet är ett absolut värde.

Klassiska varnings regler för AnonymousThrottlingError, SASThrottlingError och ThrottlingError måste delas upp i två nya aviseringar eftersom det inte finns något kombinerat mått som ger samma funktioner. Tröskelvärden måste anpassas på lämpligt sätt.

### <a name="cosmos-db-metrics"></a>Cosmos DB-mått

Alla klassiska varningar om Cosmos DB mått kan migreras utom aviseringar för dessa mått:

- Genomsnittligt antal begär Anden per sekund
- Konsekvensnivå
- Http-2xx
- Http-3xx
- Maximalt antal förbrukade RUPM per minut
- Max ru: er per sekund
- Mongo annan begär ande avgift
- Mongo annan begär ande frekvens
- Observerad Läs fördröjning
- Observerad Skriv fördröjning
- Tjänst tillgänglighet
- Lagringskapacitet

Genomsnittligt antal begär Anden per sekund, konsekvens nivå, Max RUPM förbrukat per minut, Max ru: er per sekund, observerad Läs fördröjning, observerad Skriv fördröjning och lagrings kapacitet är för närvarande inte tillgängligt i det [nya systemet](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Aviseringar på begär ande mått som http-2xx, http-3xx och tjänst tillgänglighet migreras inte eftersom det sätt som begär Anden räknas skiljer sig mellan klassiska mått och nya mått. Aviseringar för dessa mått måste återskapas manuellt med tröskelvärdena justerade.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Klassiska varnings regler för inaktuella mått

Följande är klassiska varnings regler för mått som tidigare stöddes men som slutligen är föråldrade. En liten procent andel av kunden kan ha ogiltiga klassiska aviserings regler för sådana mått. Eftersom dessa varnings regler är ogiltiga migreras de inte.

| Resurstyp| Föråldrade mått |
|-------------|----------------- |
| Microsoft. DBforMySQL/servers | compute_consumption_percent compute_limit |
| Microsoft. DBforPostgreSQL/servers | compute_consumption_percent compute_limit |
| Microsoft. Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft. SQL/Servers/databaser | service_level_objective, storage_limit, storage_used, begränsning, dtu_consumption_percent, storage_used |
| Microsoft. Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft. Web/hostingEnvironments/workerpools | BytesReceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Hur motsvarande nya varnings regler och åtgärds grupper skapas

Migreringsverktyget konverterar de klassiska varnings reglerna till motsvarande nya varnings regler och åtgärds grupper. För de flesta klassiska varnings regler finns motsvarande nya varnings regler på samma mått med samma egenskaper som `windowSize` och `aggregationType` . Det finns dock vissa klassiska varnings regler på mått som har ett annat, motsvarande mått i det nya systemet. Följande principer gäller för migrering av klassiska varningar, om inte anges i avsnittet nedan:

- **Frekvens**: anger hur ofta en klassisk eller ny varnings regel kontrollerar villkoret. `frequency`I de klassiska varnings reglerna kunde inte konfigureras av användaren och var alltid 5 minuter för alla resurs typer. Frekvensen av motsvarande regler anges också till 5 min.
- **Sammansättnings typ**: definierar hur måttet ska aggregeras över intresse fönstret. `aggregationType`Är också detsamma mellan klassiska aviseringar och nya aviseringar för de flesta mått. I vissa fall, eftersom måttet skiljer sig mellan klassiska aviseringar och nya aviseringar, `aggregationType` används motsvarande eller det `primary Aggregation Type` definierade värdet för måttet.
- **Units (units**): egenskapen för måttet som aviseringen skapas för. Vissa motsvarande mått har olika enheter. Tröskelvärdet justeras på lämpligt sätt vid behov. Om till exempel det ursprungliga måttet har sekunder som enheter men motsvarande nya mått har millisekunder som enheter multipliceras det ursprungliga tröskelvärdet med 1000 för att säkerställa samma beteende.
- **Fönster storlek**: definierar fönstret över vilka mått data aggregeras för att jämföra mot tröskelvärdet. För `windowSize` standardvärden som 5 minuter, 15 minuter, 30 minuter, 1 timme, 3 timmar, 6 timmar, 12 timmar, 1 dag, har inga ändringar gjorts för motsvarande nya aviserings regel. För andra värden används närmast närmast `windowSize` . För de flesta kunder har den här ändringen ingen inverkan. För en liten del av kunderna kan det vara nödvändigt att justera tröskelvärdet för att få exakt samma beteende.

I följande avsnitt beskriver vi de mått som har ett annat, motsvarande mått i det nya systemet. Alla mått som förblir desamma för klassiska och nya varnings regler visas inte i listan. Du kan hitta en lista över mått som stöds i det nya systemet [här](../essentials/metrics-supported.md).

### <a name="microsoftstoragestorageaccounts-and-microsoftclassicstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts och Microsoft. ClassicStorage/storageAccounts

För lagrings konto tjänster som BLOB, Table, File och Queue, mappas följande mått till motsvarande mått enligt nedan:

| Mått i klassiska aviseringar | Motsvarande mått i nya aviseringar | Kommentarer|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Transaktions mått med dimensionerna "ResponseType" = "AuthorizationError" och "Authentication" = "Anonymous"| |
| AnonymousClientOtherError | Transaktions mått med dimensionerna "ResponseType" = "ClientOtherError" och "Authentication" = "Anonymous" | |
| AnonymousClientTimeOutError| Transaktions mått med dimensionerna "ResponseType" = "ClientTimeOutError" och "Authentication" = "Anonymous" | |
| AnonymousNetworkError | Transaktions mått med dimensionerna "ResponseType" = "NetworkError" och "Authentication" = "Anonymous" | |
| AnonymousServerOtherError | Transaktions mått med dimensionerna "ResponseType" = "ServerOtherError" och "Authentication" = "Anonymous" | |
| AnonymousServerTimeOutError | Transaktions mått med dimensionerna "ResponseType" = "ServerTimeOutError" och "Authentication" = "Anonymous" | |
| AnonymousSuccess | Transaktions mått med dimensionerna "ResponseType" = "lyckades" och "autentisering" = "Anonym" | |
| AuthorizationError | Transaktions mått med dimensionerna "ResponseType" = "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Kapacitet | BlobCapacity | Använd `aggregationType` ' Average ' i stället för ' Last '. Mått gäller endast för BLOB Services |
| ClientOtherError | Transaktions mått med dimensionerna "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Transaktions mått med dimensionerna "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Använd `aggregationType` ' Average ' i stället för ' Last '. Mått gäller endast för BLOB Services |
| NetworkError | Transaktions mått med dimensionerna "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Använd `aggregationType` ' Average ' i stället för ' Last '. Mått gäller endast för BLOB Services |
| SASAuthorizationError | Transaktions mått med dimensionerna "ResponseType" = "AuthorizationError" och "Authentication" = "SAS" | |
| SASClientOtherError | Transaktions mått med dimensionerna "ResponseType" = "ClientOtherError" och "Authentication" = "SAS" | |
| SASClientTimeOutError | Transaktions mått med dimensionerna "ResponseType" = "ClientTimeOutError" och "Authentication" = "SAS" | |
| SASNetworkError | Transaktions mått med dimensionerna "ResponseType" = "NetworkError" och "Authentication" = "SAS" | |
| SASServerOtherError | Transaktions mått med dimensionerna "ResponseType" = "ServerOtherError" och "Authentication" = "SAS" | |
| SASServerTimeOutError | Transaktions mått med dimensionerna "ResponseType" = "ServerTimeOutError" och "Authentication" = "SAS" | |
| SASSuccess | Transaktions mått med dimensionerna "ResponseType" = "lyckades" och "Authentication" = "SAS" | |
| ServerOtherError | Transaktions mått med dimensionerna "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Transaktions mått med dimensionerna "ResponseType" = "ServerTimeOutError"  | |
| Klart | Transaktions mått med dimensionerna "ResponseType" = "lyckades" | |
| TotalBillableRequests| Transaktioner | |
| TotalEgress | Utgående | |
| TotalIngress | Ingress | |
| TotalRequests | Transaktioner | |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

För Cosmos DB är motsvarande mått det som visas nedan:

| Mått i klassiska aviseringar | Motsvarande mått i nya aviseringar | Kommentarer|
|--------------------------|---------------------------------|---------|
| AvailableStorage | AvailableStorage||
| Datavolym | DataUsage| |
| Antal dokument | DocumentCount||
| Index storlek | IndexUsage||
| Tjänsten är inte tillgänglig | ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||
| Begränsade begär Anden | TotalRequests med dimensionen "StatusCode" = "429"| "Genomsnittlig" agg regerings typ har korrigerats till "count"|
| Interna serverfel | TotalRequests med dimension "StatusCode" = "500"}| "Genomsnittlig" agg regerings typ har korrigerats till "count"|
| Http 401 | TotalRequests med dimensionen "StatusCode" = "401"| "Genomsnittlig" agg regerings typ har korrigerats till "count"|
| Http 400 | TotalRequests med dimensionen "StatusCode" = "400"| "Genomsnittlig" agg regerings typ har korrigerats till "count"|
| Totalt antal förfrågningar | TotalRequests| Agg regerings typen ' max ' har korrigerats till ' Count '|
| Avgift för mongo antal förfrågningar| MongoRequestCharge med dimension "CommandName" = "count"||
| Begär ande frekvens för mongo antal | MongoRequestsCount med dimension "CommandName" = "count"||
| Mongo ta bort begär ande avgift | MongoRequestCharge med dimensionen "CommandName" = "Delete"||
| Mongo ta bort begär ande frekvens | MongoRequestsCount med dimensionen "CommandName" = "Delete"||
| Mongo infoga begär ande avgift | MongoRequestCharge med dimension "CommandName" = "Infoga"||
| Mongo infoga begär ande frekvens | MongoRequestsCount med dimension "CommandName" = "Infoga"||
| Mongo för förfrågan | MongoRequestCharge med dimensionen "CommandName" = "Find"||
| Mongo för förfrågningar | MongoRequestsCount med dimensionen "CommandName" = "Find"||
| Avgift för mongo uppdaterings begär Anden | MongoRequestCharge med dimension "CommandName" = "uppdatera"||
| Mongo infogning av misslyckade begär Anden | MongoRequestCount med dimensionerna "CommandName" = "Insert" och "status" = "misslyckades"| "Genomsnittlig" agg regerings typ har korrigerats till "count"|
| Misslyckade förfrågningar för mongo-fråga | MongoRequestCount med dimensionerna "CommandName" = "fråga" och "status" = "misslyckades"| "Genomsnittlig" agg regerings typ har korrigerats till "count"|
| Antal misslyckade begär Anden om mongo | MongoRequestCount med dimensionerna "CommandName" = "count" och "status" = "misslyckades"| "Genomsnittlig" agg regerings typ har korrigerats till "count"|
| Misslyckade begär Anden om uppdatering av mongo | MongoRequestCount med dimensionerna "CommandName" = "Update" och "status" = "misslyckades"| "Genomsnittlig" agg regerings typ har korrigerats till "count"|
| Mongo andra misslyckade förfrågningar | MongoRequestCount med dimensionerna "CommandName" = "Övrigt" och "status" = "misslyckades"| "Genomsnittlig" agg regerings typ har korrigerats till "count"|
| Mongo ta bort misslyckade förfrågningar | MongoRequestCount med dimensionerna "CommandName" = "Delete" och "status" = "misslyckades"| "Genomsnittlig" agg regerings typ har korrigerats till "count"|

### <a name="how-equivalent-action-groups-are-created"></a>Hur motsvarande åtgärds grupper skapas

De klassiska varnings reglerna hade e-post, webhook, Logic app och Runbook-åtgärder som är kopplade till själva varnings regeln. Nya varnings regler använder åtgärds grupper som kan återanvändas över flera aviserings regler. Migreringsverktyget skapar en enda åtgärds grupp för samma åtgärder oavsett hur många varnings regler som använder åtgärden. Åtgärds grupper som skapats av Migreringsverktyget använder namngivnings formatet Migrated_AG *.

> [!NOTE]
> Klassiska varningar skickade lokaliserade e-postmeddelanden baserat på de nationella inställningarna för klassisk administratör när de används för att meddela klassiska administratörs roller. Nya e-postaviseringar skickas via åtgärds grupper och finns bara på engelska.

## <a name="rollout-phases"></a>Distributionsfaser

Migrations verktyget distribueras i faser till kunder som använder klassiska aviserings regler. Prenumerations ägare får ett e-postmeddelande när prenumerationen är redo att migreras med verktyget.

> [!NOTE]
> Eftersom verktyget lanseras i faser kan du se att några av dina prenumerationer ännu inte är redo att migreras under de tidiga faserna.

De flesta prenumerationer är för närvarande markerade som klara för migrering. Endast prenumerationer som har klassiska aviseringar på följande resurs typer är fortfarande inte klara för migrering.

- Microsoft. classicCompute/domän namn/platser/roller
- Microsoft. Insights/komponenter

## <a name="who-can-trigger-the-migration"></a>Vem kan utlösa migreringen?

Alla användare som har den inbyggda rollen som övervakar deltagare på prenumerations nivån kan utlösa migreringen. Användare som har en anpassad roll med följande behörigheter kan även utlösa migreringen:

- */read
- Microsoft. Insights/actiongroups/*
- Microsoft. Insights/AlertRules/*
- Microsoft. Insights/metricAlerts/*
- Microsoft. AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Förutom att ha över behörigheter bör din prenumeration Dessutom registreras med Microsoft. AlertsManagement Resource Provider. Detta krävs för att kunna migrera fel avvikelse aviseringar på Application Insights. 

## <a name="common-problems-and-remedies"></a>Vanliga problem och lösningar

När du har [utlöst migreringen](alerts-using-migration-tool.md)får du e-post till de adresser du angav för att meddela dig att migreringen är klar eller om någon åtgärd krävs från dig. I det här avsnittet beskrivs några vanliga problem och hur du hanterar dem.

### <a name="validation-failed"></a>Verifieringen misslyckades

På grund av några nya ändringar i de klassiska varnings reglerna i din prenumeration går det inte att migrera prenumerationen. Det här problemet är tillfälligt. Du kan starta om migreringen när migrerings statusen har flyttats tillbaka **för migrering** på några få dagar.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Områdes lås som hindrar oss från att migrera dina regler

Som en del av migreringen skapas nya mått varningar och nya åtgärds grupper och sedan tas de klassiska varnings reglerna bort. Ett områdes lås kan dock hindra oss från att skapa eller ta bort resurser. Beroende på omfångs låset kan vissa eller alla regler inte migreras. Du kan lösa det här problemet genom att ta bort omfångs låset för prenumerationen, resurs gruppen eller resursen, som visas i [migreringsverktyget](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)och utlösa migreringen igen. Det går inte att inaktivera områdes låset och det måste tas bort under migreringsprocessen. [Läs mer om hur du hanterar områdes lås](../../azure-resource-manager/management/lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Princip med "Neka"-inverkan som hindrar oss från att migrera dina regler

Som en del av migreringen skapas nya mått varningar och nya åtgärds grupper och sedan tas de klassiska varnings reglerna bort. Men en [Azure policy](../../governance/policy/index.yml) tilldelning kan hindra oss från att skapa resurser. Vissa eller alla regler kan inte migreras beroende på princip tilldelningen. Princip tilldelningarna som blockerar processen visas i listan i [migreringen](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Lös det här problemet genom att antingen:

- Exklusive prenumerationer, resurs grupper eller enskilda resurser under migreringen från princip tilldelningen. [Läs mer om hur du hanterar undantags omfattningar för principer](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion).
- Ange tvingande läge till **inaktiverat** för princip tilldelningen. [Läs mer om princip tilldelningens enforcementMode-egenskap](../../governance/policy/concepts/assignment-structure.md#enforcement-mode).
- Ange ett Azure Policy undantag (för hands version) för prenumerationer, resurs grupper eller enskilda resurser i princip tilldelningen. [Läs mer om strukturen för Azure policy undantag](../../governance/policy/concepts/exemption-structure.md).
- Borttagning eller ändring av effekter till "inaktive rad", "audit", "append" eller "Modify" (som till exempel kan lösa problem som är relaterade till taggar som saknas). [Läs mer om hur du hanterar princip effekter](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Nästa steg

- [Använda migreringsverktyget](alerts-using-migration-tool.md)
- [Förbereda för migrering](alerts-prepare-migration.md)