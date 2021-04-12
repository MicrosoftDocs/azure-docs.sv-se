---
title: Använd Azure Log Analytics för att samla in och visualisera mått och loggar (för hands version)
description: Lär dig hur du aktiverar det inbyggda Azure Log Analytics-Synapse som du kan använda för att samla in och skicka Apache Spark program mått och loggar till din Azure Log Analytics-arbetsyta.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: 243618192593d93bba9d5229e7becfb2af62ce32
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108567"
---
# <a name="tutorial-use-azure-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>Självstudie: Använd Azure Log Analytics för att samla in och visualisera mått och loggar (för hands version)

I den här självstudien får du lära dig hur du aktiverar den inbyggda Azure Log Analytics-Synapse som du kan använda för att samla in och skicka Apache Spark program mått och loggar till din [Azure Log Analytics-arbetsyta](/azure/azure-monitor/logs/quick-create-workspace). Du kan sedan använda en arbets bok i Azure Monitor för att visualisera mått och loggar.

## <a name="configure-azure-log-analytics-workspace-information-in-synapse-studio"></a>Konfigurera information om Azure Log Analytics-arbetsytan i Synapse Studio

### <a name="step-1-create-an-azure-log-analytics-workspace"></a>Steg 1: skapa en Azure Log Analytics-arbetsyta

Du kan följa nedanstående dokument för att skapa en Log Analytics arbets yta:
- [Skapa en Log Analytics-arbetsyta i Azure-portalen](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace)
- [Skapa en Log Analytics-arbetsyta med Azure CLI](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace-cli)
- [Skapa och konfigurera en Log Analytics arbets yta i Azure Monitor med PowerShell](https://docs.microsoft.com/azure/azure-monitor/logs/powershell-workspace-configuration)

### <a name="step-2-prepare-a-spark-configuration-file"></a>Steg 2: Förbered en spark-konfigurationsfil

#### <a name="option-1-configure-with-azure-log-analytics-workspace-id-and-key"></a>Alternativ 1. Konfigurera med ID och nyckel för Azure Log Analytics-arbetsytan 

Kopiera följande Spark-konfiguration, spara den som **"spark_loganalytics_conf.txt"** och fyll i parametrarna:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Azure Log Analytics arbetsyte-ID.
   - `<LOG_ANALYTICS_WORKSPACE_KEY>`: Azure Log Analytics nyckel: **Azure Portal > azure Log Analytics arbets yta > agent hantering > primär nyckel**

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-an-azure-key-vault"></a>Alternativ 2. Konfigurera med en Azure Key Vault

> [!NOTE]
>
> Du måste bevilja Läs behörighet till de användare som ska skicka Spark-program. Se [ge åtkomst till Key Vault nycklar, certifikat och hemligheter med en rollbaserad åtkomst kontroll i Azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide) .

Följ stegen nedan om du vill konfigurera en Azure Key Vault för att lagra arbets ytans nyckel:

1. Skapa och navigera till ditt nyckel valv i Azure Portal
2. På sidan Key Vault inställningar väljer du **hemligheter**.
3. Klicka på **Generera/importera**.
4. Välj följande värden på skärmen **Skapa en hemlighet**:
   - **Namn**: Ange ett namn för hemligheten, ange `"SparkLogAnalyticsSecret"` som standard.
   - **Värde**: ange **<LOG_ANALYTICS_WORKSPACE_KEY>** för hemligheten.
   - Lämna standardvärdena för de andra alternativen. Klicka på **Skapa**.
5. Kopiera följande Spark-konfiguration, spara den som **"spark_loganalytics_conf.txt"** och fyll i parametrarna:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Azure Log Analytics arbetsyte-ID.
   - `<AZURE_KEY_VAULT_NAME>`: Det Azure Key Vault namn som du har konfigurerat.
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>` (Valfritt): det hemliga namnet i Azure Key Vault för arbets ytans nyckel, standard: "SparkLogAnalyticsSecret".

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
>
> Du kan också lagra Log Analytics arbetsyte-ID: t i Azure Key Vault. Se stegen ovan och lagra arbetsyte-ID: t med hemligt namn `"SparkLogAnalyticsWorkspaceId"` . Eller Använd config `spark.synapse.logAnalytics.keyVault.key.workspaceId` för att ange det hemliga namnet för arbetsyte-ID i Azure Key Vault.

#### <a name="option-3-configure-with-an-azure-key-vault-linked-service"></a>Alternativ 3. Konfigurera med en Azure Key Vault länkad tjänst

> [!NOTE]
>
> Du måste bevilja Läs behörighet till Synapse-arbetsytan. Se [ge åtkomst till Key Vault nycklar, certifikat och hemligheter med en rollbaserad åtkomst kontroll i Azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide) .

Följ stegen nedan om du vill konfigurera en Azure Key Vault länkad tjänst i Synapse Studio för att lagra arbets ytans nyckel:

1. Följ alla steg i `Option 2. Configure with an Azure Key Vault` avsnittet.
2. Skapa en länkad Azure Key Vault-tjänst i Synapse Studio:

    a. Gå till **Synapse Studio > hantera > länkade tjänster**, klicka på knappen **nytt** .

    b. Sök **Azure Key Vault** i sökrutan.

    c. Ange ett namn för den länkade tjänsten.

    d. Välj Azure Key Vault. Klicka på **Skapa**.

3. Lägg till ett `spark.synapse.logAnalytics.keyVault.linkedServiceName` objekt i Spark-konfigurationen.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>Tillgänglig Spark-konfiguration

| Konfigurations namn                                  | Standardvärde                | Beskrivning                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Spark. Synapse. logAnalytics. Enabled                  | falskt                        | Om du vill aktivera Azure Log Analytics-mottagare för Spark-programmen True. Annars FALSE.                                                                                                                  |
| Spark. Synapse. logAnalytics. workspaceId              | -                            | Mål Azure-Log Analytics arbetsyte-ID                                                                                                                                                          |
| Spark. Synapse. logAnalytics. Secret                   | -                            | Målet Azure Log Analytics ytans hemlighet.                                                                                                                                                      |
| Spark. Synapse. logAnalytics. nyckel valv. linkedServiceName   | -                            | Azure Key Vault-länkat tjänst namn för Azure Log Analytics arbetsyte-ID och nyckel                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Azure Key Vault-namn för Azure Log Analytics ID och nyckel                                                                                                                                                |
| Spark. Synapse. logAnalytics. Key Vault. Key. workspaceId | SparkLogAnalyticsWorkspaceId | Azure Key Vault-hemligt namn för Azure Log Analytics arbetsyte-ID                                                                                                                                       |
| Spark. Synapse. logAnalytics. Key Vault. Key. Secret      | SparkLogAnalyticsSecret      | Azure Key Vault-hemligt namn för Azure Log Analytics Workspace-nyckeln                                                                                                                                      |
| Spark. Synapse. logAnalytics. nyckel valv. uriSuffix       | ods.opinsights.azure.com     | Målets [URI-suffix][uri_suffix]för Azure Log Analytics-arbetsyta. Om din Azure Log Analytics-arbetsyta inte finns i Azure Global måste du uppdatera URI-suffixet enligt respektive moln. |

> [!NOTE]  
> - För Azure Kina-moln ska parametern "Spark. Synapse. logAnalytics. nyckel valv. uriSuffix" vara "ods.opinsights.azure.cn". 
> - För Azure gov-moln ska parametern Spark. Synapse. logAnalytics. nyckel valv. uriSuffix vara "ods.opinsights.azure.us". 

[uri_suffix]: https://docs.microsoft.com/azure/azure-monitor/logs/data-collector-api#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>Steg 3: Ladda upp Spark-konfigurationen till en spark-pool
Du kan ladda upp konfigurations filen till Synapse Spark-poolen i Synapse Studio.

   1. Navigera till Apache Spark-poolen i Azure Synapse Studio (hantera-> Apache Spark pooler)
   2. Klicka på knappen **"..."** till höger om din Apache Spark-pool
   3. Välj Apache Spark konfiguration 
   4. Klicka på **överför** och välj **spark_loganalytics_conf.txt** som skapats.
   5. Klicka på **överför** och **Använd**.

      > [!div class="mx-imgBorder"]
      > ![konfiguration av Spark-pool](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> Alla Spark-program som skickas till Spark-poolen ovan använder konfigurations inställningen för att skicka Spark-programmets mått och loggar till din angivna Azure Log Analytics-arbetsyta.

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics-in-azure-log-analytics"></a>Skicka ett Spark-program och visa loggarna och måtten i Azure Log Analytics

 1. Du kan skicka ett Spark-program till Spark-poolen som kon figurer ATS i föregående steg på något av följande sätt:
    - Kör en Synapse Studio-anteckningsbok. 
    - Skicka en Synapse Apache Spark batch-jobb via Spark Job definition.
    - Kör en pipeline som innehåller Spark-aktivitet.

 2. Gå till den angivna Azure Log Analytics-arbetsytan och Visa sedan program måtten och loggarna när Spark-programmet börjar köras.

## <a name="use-the-sample-azure-log-analytics-workbook-to-visualize-the-metrics-and-logs"></a>Använd Azures exempel arbets bok för Log Analytics för att visualisera mått och loggar

1. [Hämta arbets boken](https://aka.ms/SynapseSparkLogAnalyticsWorkbook) här.
2. Öppna och **Kopiera** arbets bokens fil innehåll.
3. Gå till Azure Log Analytics arbets bok ([Azure Portal](https://portal.azure.com/) > Log Analytics arbets yta > arbets böcker)
4. Öppna den **"tomma"** Azure Log Analytics-arbetsboken i **"avancerad redigerare"** läge (tryck på </>-ikonen).
5. **Klistra in** över alla JSON-typer som finns.
6. Tryck sedan på **Verkställ** och sedan på **färdig redigering**.

    > [!div class="mx-imgBorder"]
    > ![ny arbets bok](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![Importera arbets bok](./media/apache-spark-azure-log-analytics/import-workbook.png)

Skicka sedan Apache Spark-programmet till den konfigurerade Spark-poolen. När programmet går till kör tillstånd väljer du det program som körs i list rutan arbets bok.

> [!div class="mx-imgBorder"]
> ![arbets bok imange](./media/apache-spark-azure-log-analytics/workbook.png)

Du kan anpassa arbets boken genom att Kusto fråga och konfigurera aviseringar.

> [!div class="mx-imgBorder"]
> ![kusto fråga och aviseringar](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

1. Exempel på frågor om Spark-händelser.

   ```kusto
   SparkListenerEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100 
   ```

2. Exempel på frågor om Spark-programdrivrutin och körnings loggar.

   ```kusto
   SparkLoggingEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100
   ```

3. Exempel på frågor om Spark-mått.

   ```kusto
   SparkMetrics_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | where name_s endswith "jvm.total.used"
   | summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
   | order by TimeGenerated asc
   ```

## <a name="create-and-manage-alerts-using-azure-log-analytics"></a>Skapa och hantera aviseringar med hjälp av Azure Log Analytics

Azure Monitor-aviseringar låter användare använda en Log Analytics-fråga för att utvärdera mått och loggar varje uppsättnings frekvens och utlösa en avisering baserat på resultaten.

Mer information finns i [skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log).

## <a name="limitation"></a>Begränsning

 - Azure Synapse Analytics-arbetsytan med [hanterat virtuellt nätverk](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-vnet) stöds inte.
 - Följande regioner stöds inte för närvarande:
   - USA, östra 2
   - Östra Norge
   - Förenade Arabemiraten, norra

## <a name="next-steps"></a>Nästa steg

 - Lär dig hur du [använder Server lös Apache Spark pool i Synapse Studio](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio).
 - Lär dig hur du [kör ett Spark-program i antecknings boken](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-development-using-notebooks).
 - Lär dig hur du [skapar Apache Spark jobb definition i Synapse Studio](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-job-definitions).