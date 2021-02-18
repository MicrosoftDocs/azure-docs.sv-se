---
title: Konfigurera BYOS (ta med din egen lagring) för profiler & Snapshot Debugger
description: Konfigurera BYOS (ta med din egen lagring) för profiler & Snapshot Debugger
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 01/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 9c3ff91cbfb6423099040a6ea46eeb66f5461f48
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589665"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>Konfigurera ta med din egen lagring (BYOS) för Application Insights Profiler och Snapshot Debugger

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>Vad är BYOS (ta med din egen lagring) och varför kan jag behöva det? 
När du använder Application Insights Profiler eller Snapshot Debugger överförs artefakter som genererats av ditt program till Azure Storage-konton via det offentliga Internet. Dessa konton betalas och kontrol leras av Microsoft för bearbetning och analys. Microsoft kontrollerar hanterings principerna för kryptering vid vila och livs längd för dessa artefakter.

Med ta med din egen lagring laddas dessa artefakter upp i ett lagrings konto som du styr. Det innebär att du styr principen för kryptering vid vila, livs längds hanterings principen och nätverks åtkomst. Du kommer dock att vara ansvarig för kostnaderna som är kopplade till det lagrings kontot.

> [!NOTE]
> Om du aktiverar privat länk är det nödvändigt att ta med din egen lagring. Mer information om privat länk för Application Insights [finns i dokumentationen.](../logs/private-link-security.md)
>
> Om du aktiverar Customer-Managed nycklar är det ett krav för att ta med din egen lagring. Mer information om Customer-Managed nycklar för Application Insights [finns i dokumentationen.](../logs/customer-managed-keys.md).

## <a name="how-will-my-storage-account-be-accessed"></a>Hur används mitt lagrings konto?
1. Agenter som körs i Virtual Machines eller App Service överför artefakter (profiler, ögonblicks bilder och symboler) till BLOB-behållare i ditt konto. Den här processen innebär att kontakta Application Insights Profiler-eller Snapshot Debuggers tjänsten för att få en SAS-token (signatur för delad åtkomst) till en ny BLOB i ditt lagrings konto.
1. Application Insights Profiler-eller Snapshot Debuggers tjänsten analyserar inkommande blob och skriver tillbaka analys resultaten och loggfilerna till Blob Storage. Beroende på tillgänglig beräknings kapacitet kan den här processen inträffa när den har laddats upp.
1. När du visar profilerings spårningen eller en ögonblicks bilds fel söknings analys, kommer tjänsten att hämta analys resultaten från Blob Storage.

## <a name="prerequisites"></a>Förutsättningar
* Se till att skapa ditt lagrings konto på samma plats som din Application Insights-resurs. Till exempel Om din Application Insights-resurs ligger i USA, västra 2, måste ditt lagrings konto även vara i västra USA 2. 
* Ge rollen "Storage BLOB data Contributor" till AAD-programmet "betrodd lagring för åtkomst till tillförlitliga enheter" i ditt lagrings konto via användar gränssnittet för Access Control (IAM).
* Om den privata länken är aktive rad konfigurerar du ytterligare inställningen för att tillåta anslutning till vår betrodda Microsoft-tjänst från din Virtual Network. 

## <a name="how-to-enable-byos"></a>Så här aktiverar du BYOS

### <a name="create-storage-account"></a>Skapa lagringskonto
Skapa ett helt nytt lagrings konto (om du inte har det) på samma plats som din Application Insights-resurs.
Om din Application Insights resurs den finns på `West US 2` , måste ditt lagrings konto vara i `West US 2` .

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>Bevilja åtkomst till diagnostiska tjänster till ditt lagrings konto
Ett BYOS lagrings konto kommer att länkas till en Application Insights-resurs. Det får bara finnas ett lagrings konto per Application Insights resurs och båda måste finnas på samma plats. Du kan använda samma lagrings konto med fler än en Application Insights-resurs.

Först måste Application Insights Profiler och Snapshot Debugger-tjänsten beviljas åtkomst till lagrings kontot. Om du vill bevilja åtkomst lägger du till rollen `Storage Blob Data Contributor` i AAD-appen med namnet `Diagnostic Services Trusted Storage Access` via Access Control (IAM)-sidan i ditt lagrings konto, som visas i bild 1,0. 

Steg: 
1. Klicka på knappen Lägg till i avsnittet Lägg till en roll tilldelning 
1. Välj rollen "Storage BLOB data Contributor" 
1. Välj "Azure AD User, Group eller service huvud namn" i avsnittet "tilldela åtkomst till" 
1. Sök & Välj appen "Diagnostic Services Trusted Storage Access" 
1. Spara ändringar

_![ Figur 1,0](media/profiler-bring-your-own-storage/figure-10.png)_ 
 _figur 1,0_ 

När du har lagt till rollen visas den under avsnittet "roll tilldelningar", precis som i bild 1,1. 
_![ Figur 1,1](media/profiler-bring-your-own-storage/figure-11.png)_ 
 _figur 1,1_ 

Om du också använder en privat länk krävs en ytterligare konfiguration för att tillåta anslutning till vår betrodda Microsoft-tjänst från din Virtual Network. Läs dokumentationen om [lagrings nätverks säkerhet](../../storage/common/storage-network-security.md#trusted-microsoft-services).

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>Länka ditt lagrings konto till din Application Insights-resurs
Det finns tre alternativ för att konfigurera BYOS för kod på kod nivå (profilerings-/fel sökning):

* Använda Azure PowerShell-cmdletar
* Använda kommando rads gränssnittet i Azure (CLI)
* Använda Azure Resource Manager-mall

#### <a name="configure-using-azure-powershell-cmdlets"></a>Konfigurera med hjälp av Azure PowerShell-cmdletar

1. Kontrol lera att du har installerat AZ PowerShell-4.2.0 eller senare.

    Information om hur du installerar Azure PowerShell finns i den [officiella Azure PowerShell dokumentationen](/powershell/azure/install-az-ps).

1. Installera Application Insights PowerShell-tillägget.
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. Logga in med ditt Azure-konto
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    Mer information om hur du loggar in finns i [dokumentationen för Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

1. Ta bort det tidigare lagrings kontot som är kopplat till Application Insights resursen.

    Ofta
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    Exempel:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. Anslut ditt lagrings konto till din Application Insights-resurs.
    
    Ofta
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    Exempel:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>Konfigurera med Azure CLI

1. Kontrol lera att du har installerat Azure CLI.

    Information om hur du installerar Azure CLI finns i den [officiella Azure CLI-dokumentationen](/cli/azure/install-azure-cli).

1. Installera Application Insights CLI-tillägget.
    ```powershell
    az extension add -n application-insights
    ```

1. Anslut ditt lagrings konto till din Application Insights-resurs.

    Ofta
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    Exempel:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    Förväntad utdata:
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > Information om hur du utför uppdateringar på länkade lagrings konton till din Application Insights-resurs finns i [Application Insights CLI-dokumentationen](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage).

#### <a name="configure-using-azure-resource-manager-template"></a>Konfigurera med Azure Resource Manager mall

1. Skapa en Azure Resource Manager mallfil med följande innehåll (byos.template.jspå).
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Kör följande PowerShell-kommando för att distribuera tidigare mall (skapa länkat lagrings konto).

    Ofta
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    Exempel:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. Ange följande parametrar när du uppmanas till PowerShell-konsolen:
    
    |           Parameter           |                                Beskrivning                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | Namnet på Application Insights resursen för att aktivera BYOS.            |
    | storage_account_name          | Namnet på den lagrings konto resurs som du ska använda som BYOS. |
    
    Förväntad utdata:
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. Aktivera diagnostik på kod nivå (profilerings-/fel sökning) på arbets belastningen som intresserar dig genom Azure Portal. (App Service > Application Insights) _![ Figur 2,0](media/profiler-bring-your-own-storage/figure-20.png)_ 
 _figur 2,0_

## <a name="troubleshooting"></a>Felsökning
### <a name="template-schema-schema_uri-isnt-supported"></a>Mallnamnet {schema_uri} stöds inte.
* Kontrol lera att `$schema` mallens egenskap är giltig. Det måste följa följande mönster: `https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* Kontrol lera att `schema_version` mallen är inom giltiga värden: `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` .
    Felmeddelande:
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>Ingen registrerad resurs leverantör hittades för platsen {location}.
* Kontrol lera att `apiVersion` resursen `microsoft.insights/components` är `2015-05-01` .
* Kontrol lera att `apiVersion` resursen `linkedStorageAccount` är `2020-03-01-preview` .
    Felmeddelande:
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>Lagrings konto platsen måste matcha AI-komponentens plats.
* Kontrol lera att platsen för den Application Insights resursen är samma som lagrings kontot.
    Felmeddelande:
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

För allmän fel sökning av profiler, se [fel söknings dokumentation för profiler](profiler-troubleshooting.md).

Allmän Snapshot Debugger fel sökning finns i [Snapshot debugger fel söknings dokumentation](snapshot-debugger-troubleshoot.md). 

## <a name="faqs"></a>Vanliga frågor och svar
* Om jag har profiler eller ögonblicks bild aktiverat, och sedan jag aktiverade BYOS, kommer mina data att migreras till mitt lagrings konto?
    _Nej, det fungerar inte._

* Kommer BYOS att fungera med kryptering i vila och Customer-Managed nyckel?
    _Ja, för att vara exakt måste BYOS ha profilerings-eller fel sökning aktive rad med Customer-Manager nycklar._

* Kommer BYOS att fungera i en miljö som är isolerad från Internet?
    _Ja. I själva verket är BYOS ett krav för isolerade nätverks scenarier._

* Kommer BYOS att fungera när, båda, Customer-Managed nycklar och privat länk har Aktiver ATS? 
    _Ja, det kan vara möjligt._

* Om jag har aktiverat BYOS kan jag gå tillbaka med hjälp av diagnostiska tjänster lagrings konton för att lagra insamlade data? 
    _Ja, men nu har vi inte stöd för migrering av data från din BYOS._

* När du har aktiverat BYOS tar jag över alla relaterade kostnader för den, vilket är lagring och nätverk? 
    _Ja_
