---
title: Azure Key Vault lösning i Azure Monitor | Microsoft Docs
description: Du kan använda Azure Key Vault lösning i Azure Monitor för att granska Azure Key Vault loggar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: f9b265ba8970d9f065cc13cff1e5faeb466ac570
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577497"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Key Vault Analytics-lösning i Azure Monitor

> [!NOTE]
> Den här lösningen är föråldrad. [Vi rekommenderar nu att du använder Azure Monitor för Key Vault](./key-vault-insights-overview.md).

![Key Vault symbol](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan använda Azure Key Vault-lösningen i Azure Monitor för att granska Azure Key Vault AuditEvent-loggar.

Om du vill använda lösningen måste du aktivera loggning av Azure Key Vault diagnostik och dirigera diagnostiken till en Log Analytics-arbetsyta. Du behöver inte skriva loggarna till Azure Blob Storage.

> [!NOTE]
> I januari 2017 har det stödda sättet att skicka loggar från Key Vault till Log Analytics ändrats. Om Key Vault lösning som du använder visas *(inaktuell)* i rubriken, se [Migrera från den gamla Key Vault-lösningen](#migrating-from-the-old-key-vault-solution) för steg som du måste följa.
>
>

## <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande instruktioner för att installera och konfigurera Azure Key Vault-lösningen:

1. Använd processen som beskrivs i [Lägg till Azure Monitor lösningar från Lösningsgalleriet](./solutions.md) för att lägga till Azure Key Vault-lösningen i Log Analytics-arbetsytan.
2. Aktivera diagnostikloggning för Key Vault resurser som ska övervakas med hjälp av antingen [portalen](#enable-key-vault-diagnostics-in-the-portal) eller [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Aktivera Key Vault diagnostik i portalen

1. I Azure Portal navigerar du till den Key Vault resurs som ska övervakas
2. Välj *diagnostikinställningar* för att öppna följande sida

   ![Skärm bild av sidan diagnostikinställningar för Key Vault Resource ContosoKVSCUS. alternativet för att aktivera diagnostik är markerat.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Klicka på *Aktivera diagnostik* för att öppna följande sida

   ![Skärm bild av sidan för att konfigurera diagnostikinställningar. Alternativen för att skicka till Log Analytics, AuditEvent logg och AllMetrics är markerade.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Ange ett namn på den diagnostiska inställningen.
5. Klicka i kryss rutan för *att skicka till Log Analytics*
6. Välj en befintlig Log Analytics arbets yta eller skapa en arbets yta
7. Om du vill aktivera *AuditEvent* loggar klickar du på kryss rutan under loggen
8. Klicka på *Spara* för att aktivera loggning av diagnostik till Log Analytics arbets ytan.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Aktivera Key Vault diagnostik med PowerShell
Följande PowerShell-skript innehåller ett exempel på hur du kan använda `Set-AzDiagnosticSetting` för att aktivera resurs loggning för Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Granska Azure Key Vault information om data insamling
Azure Key Vault lösning samlar in diagnostikloggar direkt från Key Vault.
Du behöver inte skriva loggarna till Azure Blob Storage och ingen agent krävs för data insamling.

I följande tabell visas metoder för data insamling och annan information om hur data samlas in för Azure Key Vault.

| Plattform | Direkt agent | System Center Operations Manager agent | Azure | Operations Manager krävs. | Operations Manager agent data som skickas via hanterings gruppen | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | vid ankomsten |

## <a name="use-azure-key-vault"></a>Använda Azure Key Vault
När du har [installerat lösningen](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?source=intercept.nl&tab=Overview)kan du Visa Key Vault data genom att klicka på panelen **Key Vault-analys** på sidan Azure Monitor **Översikt** . Öppna den här sidan från **Azure Monitor** -menyn genom att klicka på **mer** under avsnittet om **insikter** . 

![Skärm bild av panelen Key Vault-analys på översikts sidan för Azure Monitor som visar ett diagram över Operations volym för nyckel valvet över tid.](media/azure-key-vault/log-analytics-keyvault-tile.png)

När du klickar på panelen **Key Vault-analys** kan du Visa sammanfattningar av loggarna och sedan gå vidare till information för följande kategorier:

* Volym för alla Key Vault-åtgärder över tid
* Misslyckade åtgärds volymer över tid
* Genomsnittlig drift svars tid per åtgärd
* Tjänst kvalitet för åtgärder med antalet åtgärder som tar mer än 1000 MS och en lista över åtgärder som tar mer än 1000 MS

![Skärm bild av Azure Key Vault instrument panel som visar paneler med grafik data för alla åtgärder, misslyckade åtgärder och genomsnittlig drifts fördröjning.](media/azure-key-vault/log-analytics-keyvault01.png)

![Skärm bild av instrument panelen Azure Key Vault som visar paneler med data för genomsnittlig drifts svars tid, tjänst kvalitet och rekommenderade sökningar.](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Så här visar du information om alla åtgärder
1. På sidan **Översikt** klickar du på panelen **Key Vault-analys** .
2. På instrument panelen **Azure Key Vault** granskar du sammanfattnings informationen på ett av bladet och klickar sedan på en för att visa detaljerad information om den på sidan för loggs ökning.

    På någon av logg Sök sidorna kan du Visa resultat efter tid, detaljerade resultat och logg Sök historik. Du kan också filtrera efter FACET för att begränsa resultaten.

## <a name="azure-monitor-log-records"></a>Azure Monitor logg poster
Azure Key Vault-lösningen analyserar poster som har en typ av nyckel **valv** som samlas in från [AuditEvent-loggar](../../key-vault/general/logging.md) i Azure-diagnostik.  Egenskaperna för dessa poster finns i följande tabell:  

| Egenskap | Beskrivning |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |IP-adressen för den klient som gjorde begäran |
| `Category` | *AuditEvent* |
| `CorrelationId` |Ett valfritt GUID som klienten kan skicka för att korrelera loggar på klientsidan med loggar på tjänstsidan (Key Vault). |
| `DurationMs` |Hur lång tid i millisekunder som det tog att utföra REST-API-begäran. Den här tiden omfattar inte nätverks svars tiden, så den tid som du mäter på klient sidan kanske inte matchar den här tiden. |
| `httpStatusCode_d` |HTTP-statuskod som returnerades av begäran (till exempel *200*) |
| `id_s` |Unikt ID för begäran |
| `identity_claim_appid_g` | GUID för program-ID |
| `OperationName` |Åtgärdens namn, enligt beskrivningen i [Azure Key Vault loggning](../../key-vault/general/logging.md) |
| `OperationVersion` |REST API version som begärs av klienten (till exempel *2015-06-01*) |
| `requestUri_s` |URI för begäran |
| `Resource` |Nyckel valvets namn |
| `ResourceGroup` |Nyckel valvets resurs grupp |
| `ResourceId` |Azure Resource Manager Resource-ID. För Key Vault loggar är detta Key Vault resurs-ID. |
| `ResourceProvider` |*Utforskaren. KEYVAULT* |
| `ResourceType` | *VALV* |
| `ResultSignature` |HTTP-status (till exempel *OK*) |
| `ResultType` |Resultat av REST API begäran (till exempel *lyckad*) |
| `SubscriptionId` |Prenumerations-ID för Azure som innehåller Key Vault |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrera från den gamla Key Vaults lösningen
I januari 2017 har det stödda sättet att skicka loggar från Key Vault till Log Analytics ändrats. Dessa ändringar ger följande fördelar:
+ Loggarna skrivs direkt till en Log Analytics arbets yta utan att behöva använda ett lagrings konto
+ Mindre latens från den tid då loggar genereras till dem som är tillgängliga i Log Analytics
+ Färre konfigurations steg
+ Ett vanligt format för alla typer av Azure Diagnostics

Så här använder du den uppdaterade lösningen:

1. [Konfigurera diagnostik så att den skickas direkt till en Log Analytics arbets yta från Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Aktivera Azure Key Vault-lösningen genom att använda processen som beskrivs i [Lägg till Azure Monitor lösningar från Lösningsgalleriet](./solutions.md)
3. Uppdatera eventuella sparade frågor, instrument paneler eller aviseringar för att använda den nya data typen
   + Typen är ändra från: nyckel valv till AzureDiagnostics. Du kan använda ResourceType för att filtrera till Key Vault loggar.
   + I stället för: `KeyVaults` använder du `AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Fält: (fält namn är Skift läges känsliga)
   + För alla fält med suffixet \_ s, \_ d eller \_ g i namnet ändrar du det första symbolen till gemener
   + För fält som har suffixet \_ o i namn delas data upp i enskilda fält baserat på de kapslade fält namnen. Till exempel lagras UPN för anroparen i ett fält `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + Fältet CallerIpAddress ändrades till CallerIPAddress
   + Fältet RemoteIPCountry finns inte längre
4. Ta bort den *Key Vault-analys (föråldrade)* lösningen. Om du använder PowerShell använder du `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Data som samlas in innan ändringen visas inte i den nya lösningen. Du kan fortsätta att fråga efter dessa data med hjälp av de gamla typ-och fält namnen.

## <a name="troubleshooting"></a>Felsökning
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Nästa steg
* Använd [logg frågor i Azure Monitor](../logs/log-query-overview.md) om du vill visa detaljerade Azure Key Vault data.

