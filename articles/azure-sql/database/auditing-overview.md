---
title: Azure SQL Granskning för Azure SQL Database och Azure Synapse Analytics
description: Använd Azure SQL Database för att spåra databashändelser i en granskningslogg.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/17/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: bc7ac6b97d10e5941e46b8be3e12baff32bded4a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483069"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Granskning för Azure SQL Database och Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Granskning för [Azure SQL Database](sql-database-paas-overview.md) och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) spårar databashändelser och skriver dem till en granskningslogg i ditt Azure-lagringskonto, din Log Analytics-arbetsyta eller Event Hubs.

Granskning gör även följande:

- Det hjälper dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insikter om i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

- Det främjar och underlättar uppfyllandet av efterlevnadsstandarder, även om det inte garanterar efterlevnad. Mer information om Azure-program som har stöd för standardefterlevnad finns i [Azure Säkerhetscenter](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), där du hittar den senaste listan över Azure SQL-efterlevnadscertifieringar.

> [!NOTE]
> Information om hur Azure SQL Managed Instance granskning finns i följande artikel Komma [igång med SQL Managed Instance granskning](../managed-instance/auditing-configure.md).

## <a name="overview"></a><a id="overview"></a>Översikt

Du kan använda SQL Database för att:

- **Behåll** en granskningslogg för valda händelser. Du kan definiera kategorier med databasåtgärder som ska granskas.
- **Rapportera** om databasaktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att snabbt komma igång med aktivitets- och händelserapportering.
- **Analysera** rapporter. Du kan hitta misstänkta händelser, ovanliga aktiviteter och trender.

> [!IMPORTANT]
> Granskning för Azure SQL Database och Azure Synapse är optimerad för tillgänglighet och prestanda. Under mycket hög aktivitet, eller hög nätverksbelastning, kan Azure SQL Database Azure Synapse att åtgärder kan fortsätta och kanske inte registrerar vissa granskade händelser..

### <a name="auditing-limitations"></a>Granskningsbegränsningar

- **Premium Storage** stöds inte **för närvarande.**
- **Hierarkisk namnrymd för Azure Data Lake Storage Gen2** **ett lagringskonto** **stöds inte för närvarande.**
- Att aktivera granskning på en **pausad Azure Synapse** stöds inte. Du kan aktivera granskning genom att återuppta Azure Synapse.
- Granskning för **Azure Synapse SQL-pooler** stöder endast standardåtgärdsgrupper **för granskning.**


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definiera granskningsprincip på servernivå jämfört med databasnivå

En granskningsprincip kan definieras för en specifik databas eller som en [standardserverprincip](logical-servers.md) i Azure (som är värd för SQL Database eller Azure Synapse):

- En serverprincip gäller för alla befintliga och nyligen skapade databaser på servern.

- Om *servergranskning är aktiverat* gäller det alltid för *databasen*. Databasen granskas, oavsett inställningarna för databasgranskning.

- När granskningsprincipen har definierats på databasnivå till en Log Analytics-arbetsyta eller ett Event Hub-mål behåller inte följande åtgärder källprincipen för granskning på databasnivå:
    - [Databaskopia](database-copy.md)
    - [Återställning från tidpunkt](recovery-using-backups.md)
    - [Geo-replikering](active-geo-replication-overview.md) (sekundär databas kommer inte att ha granskning på databasnivå)

- Att aktivera granskning på databasen, förutom att aktivera det  på servern, åsidosätter inte eller ändrar inte några av inställningarna för servergranskning. Båda granskningarna kommer att finnas sida vid sida. Med andra ord granskas databasen två gånger parallellt. en gång av serverprincipen och en gång av databasprincipen.

   > [!NOTE]
   > Du bör undvika att aktivera både servergranskning och databasblobgranskning tillsammans, såvida inte:
    >
    > - Du vill använda ett annat *lagringskonto,* *kvarhållningsperiod eller* *Log Analytics-arbetsyta* för en specifik databas.
    > - Du vill granska händelsetyper eller kategorier för en specifik databas som skiljer sig från resten av databaserna på servern. Du kan till exempel ha tabellinfogningar som endast behöver granskas för en specifik databas.
   >
   > Annars rekommenderar vi att du endast aktiverar granskning på servernivå och låter granskning på databasnivå vara inaktiverat för alla databaser.

#### <a name="remarks"></a>Kommentarer

- Granskningsloggar skrivs till **tilläggsblobar i** en Azure Blob Storage i din Azure-prenumeration
- Granskningsloggarna är i .xel-format och kan öppnas med [hjälp av SQL Server Management Studio (SSMS).](/sql/ssms/download-sql-server-management-studio-ssms)
- Om du vill konfigurera ett oföränderligt loggarkiv för granskningshändelser på server- eller databasnivå följer du [anvisningarna i Azure Storage](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes). Kontrollera att du har valt **Tillåt ytterligare tillägg när** du konfigurerar den oföränderliga bloblagringen.
- Du kan skriva granskningsloggar till ett Azure Storage bakom ett VNet eller en brandvägg. Specifika anvisningar finns i Skriva [granskning till ett lagringskonto bakom VNet och brandväggen](audit-write-storage-account-behind-vnet-firewall.md).
- Mer information om loggformatet, hierarkin för lagringsmappen och namngivningskonventionerna finns i [Referens för blobgranskningsloggformat.](./audit-log-format.md)
- Granskning är automatiskt aktiverat på [skrivskyddade repliker](read-scale-out.md). Mer information om hierarkin för lagringsmappar, namngivningskonventioner och loggformat finns [i SQL Database Granskningsloggformat](audit-log-format.md).
- När du använder Azure AD-autentisering visas inte misslyckade *inloggningsposter* i SQL-granskningsloggen. Om du vill visa misslyckade granskningsposter för inloggning måste du [besöka Azure Active Directory ,](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)som loggar information om dessa händelser.
- Inloggningar dirigeras av gatewayen till den instans där databasen finns.  Vid en AAD-inloggning verifieras autentiseringsuppgifterna innan ett försök görs att logga in på den begärda databasen med aktuella användaren.  Om det uppstår ett fel går det inte att komma åt den begärda databasen, vilket innebär att det inte utförs någon granskning.  När det gäller SQL-inloggningar verifieras autentiseringsuppgifterna på begärda data, så i det här fallet kan de granskas.  Lyckade inloggningar, som uppenbart når databasen, granskas i båda fallen.
- När du har konfigurerat granskningsinställningarna kan du aktivera den nya funktionen för hotidentifiering och konfigurera e-postmeddelanden för att ta emot säkerhetsaviseringar. När du använder hotidentifiering får du proaktiva aviseringar om avvikande databasaktiviteter som kan tyda på potentiella säkerhetshot. Mer information finns i Komma [igång med hotidentifiering.](threat-detection-overview.md)

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Konfigurera granskning för servern

Standardgranskningsprincipen innehåller alla åtgärder och följande åtgärdsgrupper, som granskar alla frågor och lagrade procedurer som körs mot databasen, samt lyckade och misslyckade inloggningar:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
Du kan konfigurera granskning för olika typer av åtgärder och åtgärdsgrupper med hjälp av PowerShell, enligt beskrivningen [i avsnittet Hantera SQL Database granskning med Azure PowerShell.](#manage-auditing)

Azure SQL Database och Azure Synapse Audit lagrar 4 000 tecken data för teckenfält i en granskningspost. När **instruktionen** eller **data_sensitivity_information-värden** som returneras från en granskningsbar åtgärd innehåller mer än 4 000 tecken trunkeras alla data utöver de första 4 000 tecknen och granskas **inte**.
I följande avsnitt beskrivs konfigurationen av granskning med hjälp av Azure Portal.

  > [!NOTE]
  > - Det går inte att aktivera granskning på en pausad dedikerad SQL-pool. Om du vill aktivera granskning tar du bort pausen för den dedikerade SQL-poolen. Läs mer om [dedikerad SQL-pool.](../..//synapse-analytics/sql/best-practices-dedicated-sql-pool.md)
  > - När granskning har konfigurerats för en Log Analytics-arbetsyta eller till ett Event Hub-mål via Azure Portal- eller PowerShell-cmdleten skapas en diagnostikinställning med kategorin "SQLSecurityAuditEvents" aktiverad. [](../../azure-monitor/essentials/diagnostic-settings.md)

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Gå till **Granskning** under rubriken Säkerhet i **sql-databasen eller** **sql-serverfönstret.**
3. Om du föredrar att konfigurera en servergranskningsprincip kan du välja länken **Visa serverinställningar** på sidan för databasgranskning. Du kan sedan visa eller ändra inställningarna för servergranskning. Servergranskningsprinciper gäller för alla befintliga och nyligen skapade databaser på den här servern.

    ![Skärmbild som visar länken Visa serverinställningar markerad på sidan för databasgranskning.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Om du föredrar att aktivera granskning på databasnivå växlar du **Granskning** till **PÅ**. Om servergranskning har aktiverats finns den databaskonfigurerade granskningen sida vid sida med servergranskningen.

5. Du har flera alternativ för att konfigurera var granskningsloggar ska skrivas. Du kan skriva loggar till ett Azure Storage-konto, till en Log Analytics-arbetsyta för användning via Azure Monitor-loggar eller till händelsehubben för användning med händelsehubben. Du kan konfigurera valfri kombination av dessa alternativ så skrivs granskningsloggar till var och en.
  
   ![lagringsalternativ](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>Granskning av Microsoft Support åtgärder

Granskning av Microsoft Support åtgärder för Azure SQL Server gör att du kan granska Microsofts supportteknikers åtgärder när de behöver åtkomst till servern under en supportbegäran. Användningen av den här funktionen, tillsammans med din granskning, möjliggör större transparens för personalen och möjliggör avvikelseidentifiering, trendvisualisering och dataförlustskydd.

Om du vill Microsoft Support granskningsåtgärder går du till Granskning **under** rubriken Säkerhet i **Azure SQL-serverfönstret** och växlar Granskning av **Microsoft-supportåtgärder** till **PÅ**.

  > [!IMPORTANT]
  > Granskning av Microsofts supportåtgärder stöder inte mål för lagringskontot. För att aktivera funktionen måste en Log Analytics-arbetsyta eller ett event hub-mål konfigureras.

![Skärmbild av Microsoft Support åtgärder](./media/auditing-overview/support-operations.png)

Om du vill granska granskningsloggarna Microsoft Support åtgärder i Log Analytics-arbetsytan använder du följande fråga:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Granska till lagringsmål

Om du vill konfigurera skrivning av granskningsloggar till ett **lagringskonto** väljer du Lagring när du kommer **till avsnittet** Granskning. Välj det Azure-lagringskonto där loggarna ska sparas och välj sedan kvarhållningsperioden genom att öppna **Avancerade egenskaper.** Klicka sedan på **Spara**. Loggar som är äldre än kvarhållningsperioden tas bort.

- Standardvärdet för kvarhållningsperioden är 0 (obegränsad kvarhållning). Du kan ändra det här värdet genom att flytta **skjutreglaget Kvarhållning (dagar)** **i Avancerade egenskaper** när du konfigurerar lagringskontot för granskning.
  - Om du ändrar kvarhållningsperioden från 0 (obegränsad kvarhållning) till ett annat värde, bör du observera att kvarhållningen endast gäller för loggar som skrivits efter att kvarhållningsvärdet har ändrats (loggar som skrivits under perioden när kvarhållningen var inställt på obegränsad bevaras, även efter att kvarhållning har aktiverats).

  ![storage account](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Granska till Log Analytics-mål
  
Om du vill konfigurera skrivning av granskningsloggar till en Log Analytics-arbetsyta väljer du **Log Analytics** och öppnar **Log Analytics-information.** Välj Log Analytics-arbetsytan där loggarna ska skrivas och klicka sedan på **OK.** Om du inte har skapat en Log Analytics-arbetsyta kan [du gå till Skapa en Log Analytics-arbetsyta i Azure Portal](../../azure-monitor/logs/quick-create-workspace.md).

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

Mer information om hur du Azure Monitor Log Analytics-arbetsytan finns i Designing your Azure Monitor Logs deployment (Utforma [distribution av Azure Monitor loggar)](../../azure-monitor/logs/design-logs-deployment.md)
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Granska till Händelsehubbmål

Om du vill konfigurera skrivning av granskningsloggar till en händelsehubb väljer **du Händelsehubb.** Välj den händelsehubb där loggarna ska skrivas och klicka sedan på **Spara.** Se till att händelsehubben finns i samma region som databasen och servern.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analysera granskningsloggar och rapporter

Om du väljer att skriva granskningsloggar för Azure Monitor loggar:

- Använd [Azure-portalen](https://portal.azure.com). Öppna relevant databas. Längst upp på databasens **granskningssida väljer** du Visa **granskningsloggar.**

    ![visa granskningsloggar](./media/auditing-overview/auditing-view-audit-logs.png)

- Sedan kan du visa loggarna på två sätt:

    Om du klickar på Log  **Analytics** överst på sidan Granskningsposter öppnas loggvyn i Log Analytics-arbetsytan, där du kan anpassa tidsperioden och sökfrågan.

    ![öppna i Log Analytics-arbetsytan](./media/auditing-overview/auditing-log-analytics.png)

    Om **du klickar** på  Visa instrumentpanel överst på sidan Granskningsposter öppnas en instrumentpanel som visar information om granskningsloggar, där du kan öka detaljgranska säkerhetsinsikter, åtkomst till känsliga data med mera. Den här instrumentpanelen är utformad för att hjälpa dig att få säkerhetsinsikter för dina data.
    Du kan också anpassa tidsperioden och sökfrågan.
    ![Visa Log Analytics-instrumentpanelen](media/auditing-overview/auditing-view-dashboard.png)

    ![Log Analytics-instrumentpanel](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics Security Insights](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- Du kan också komma åt granskningsloggarna från Log Analytics-bladet. Öppna Log Analytics-arbetsytan och **klicka** på Loggar under avsnittet **Allmänt.** Du kan börja med en enkel fråga, till exempel: *sök efter "SQLSecurityAuditEvents" för* att visa granskningsloggarna.
    Härifrån kan du också använda Azure Monitor [för](../../azure-monitor/logs/log-query-overview.md)  att köra avancerade sökningar på dina granskningsloggdata. Azure Monitor-loggar ger dig driftsinformation i realtid med hjälp av integrerade sök- och anpassade instrumentpaneler för att enkelt analysera miljontals poster över alla dina arbetsbelastningar och servrar. Mer användbar information om hur Azure Monitor och kommandon finns i [sökreferensen Azure Monitor för loggar.](../../azure-monitor/logs/log-query-overview.md)

Om du väljer att skriva granskningsloggar till Händelsehubb:

- Om du vill använda granskningsloggdata från händelsehubben måste du konfigurera en ström för att använda händelser och skriva dem till ett mål. Mer information finns i [dokumentationen Azure Event Hubs .](../index.yml)
- Granskningsloggar i Händelsehubb avbildas i brödtexten i [Apache Avro-händelser](https://avro.apache.org/) och lagras med JSON-formatering med UTF-8-kodning. Om du vill läsa granskningsloggarna kan du använda [Avro-verktyg](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) eller liknande verktyg som kan hantera det här formatet.

Om du väljer att skriva granskningsloggar till ett Azure Storage-konto finns det flera metoder som du kan använda för att visa loggarna:

- Granskningsloggar sammanställs i det konto som du valde under installationen. Du kan utforska granskningsloggar med hjälp av ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/). I Azure Storage sparas granskningsloggar som en samling blobfiler i en container med namnet **sqldbauditlogs**. Mer information om hierarkin för lagringsmappar, namngivningskonventioner och loggformat finns [i SQL Database Granskningsloggformat](./audit-log-format.md).

- Använd [Azure-portalen](https://portal.azure.com).  Öppna relevant databas. Längst upp på databasens granskningssida **klickar du** på Visa **granskningsloggar.**

    ![visa granskningsloggar](./media/auditing-overview/auditing-view-audit-logs.png)

    **Granskningsposter** öppnas där du kan visa loggarna.

  - Du kan visa specifika datum genom att **klicka på** Filter överst på sidan **Granskningsposter.**
  - Du kan växla mellan granskningsposter som har skapats av *serverns granskningsprincip* och *databasens granskningsprincip genom* att växla **granskningskälla**.

       ![Skärmbild som visar alternativen för att visa granskningsposterna.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- Använd systemfunktionen för **sys.fn_get_audit_file** (T-SQL) för att returnera granskningsloggdata i tabellformat. Mer information om hur du använder den här funktionen finns [i sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Använd **Sammanfoga granskningsfiler** i SQL Server Management Studio (från och med SSMS 17):
    1. På SSMS-menyn väljer du **Arkiv Öppna**  >  **sammanfoga**  >  **granskningsfiler.**

        ![Skärmbild som visar menyalternativet Sammanfoga granskningsfiler.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. Dialogrutan **Lägg till granskningsfiler** öppnas. Välj ett av Alternativen **för att** lägga till för att välja om du vill slå samman granskningsfiler från en lokal disk eller importera dem från Azure Storage. Du måste ange din Azure Storage och kontonyckel.

    3. När alla filer som ska sammanfogas har lagts till klickar du **på OK** för att slutföra sammanslagningsåtgärden.

    4. Den sammanslagna filen öppnas i SSMS, där du kan visa och analysera den, samt exportera den till en XEL- eller CSV-fil eller till en tabell.

- Använd Power BI. Du kan visa och analysera granskningsloggdata i Power BI. Mer information och åtkomst till en nedladdningsbar mall finns i [Analysera granskningsloggdata i Power BI](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895).
- Ladda ned loggfiler från din Azure Storage blobcontainer via portalen eller med hjälp av ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/).
  - När du har hämtat en loggfil lokalt dubbelklickar du på filen för att öppna, visa och analysera loggarna i SSMS.
  - Du kan också ladda ned flera filer samtidigt via Azure Storage Explorer. Det gör du genom att högerklicka på en specifik undermapp och välja **Spara som för** att spara i en lokal mapp.

- Ytterligare metoder:

  - När du har hämtat flera filer eller en undermapp som innehåller loggfiler kan du slå samman dem lokalt enligt beskrivningen i anvisningarna för granskningsfiler för SSMS-sammanslagning som beskrivs ovan.
  - Visa blobgranskningsloggar programmatiskt: [Fråga Extended Events Files](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) med hjälp av PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Produktionsmetoder


### <a name="auditing-geo-replicated-databases"></a>Granska geo-replikerade databaser

När du aktiverar granskning på den primära databasen har den sekundära databasen en identisk granskningsprincip med geo-replikerade databaser. Det är också möjligt att konfigurera granskning på den sekundära databasen genom att aktivera granskning på den **sekundära servern**, oberoende av den primära databasen.

- Servernivå (**rekommenderas):** Aktivera granskning  på både den  primära och den sekundära servern – de primära och sekundära databaserna granskas oberoende av varandra baserat på deras respektive princip på servernivå.
- Databasnivå: Granskning på databasnivå för sekundära databaser kan bara konfigureras från primära databasgranskningsinställningar.
  - Granskning måste aktiveras på själva *den primära databasen,* inte på servern.
  - När granskning har aktiverats för den primära databasen aktiveras den även på den sekundära databasen.

    > [!IMPORTANT]
    > Vid granskning på databasnivå är lagringsinställningarna för den sekundära databasen identiska med inställningarna för den primära databasen, vilket orsakar trafik mellan olika regionala databaser. Vi rekommenderar att du endast aktiverar granskning på servernivå och lämnar granskning på databasnivå inaktiverat för alla databaser.

### <a name="storage-key-regeneration"></a>Återskapa lagringsnyckel

I produktion kommer du troligen att uppdatera dina lagringsnycklar regelbundet. När du skriver granskningsloggar till Azure Storage måste du spara granskningsprincipen på nytt när du uppdaterar dina nycklar. Processen ser ut så här:

1. Öppna **Avancerade egenskaper** under **Lagring**. I rutan **Lagringsåtkomstnyckel** väljer du **Sekundär**. Klicka sedan **på** Spara överst på sidan granskningskonfiguration.

    ![Skärmbild som visar processen för att välja en sekundär lagringsåtkomstnyckel.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. Gå till sidan för lagringskonfiguration och återskapa den primära åtkomstnyckeln.

    ![Navigeringsfönster](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Gå tillbaka till sidan granskningskonfiguration växlar du lagringsåtkomstnyckeln från sekundär till primär och klickar sedan på **OK.** Klicka sedan **på** Spara överst på sidan granskningskonfiguration.
4. Gå tillbaka till sidan för lagringskonfiguration och återskapa den sekundära åtkomstnyckeln (som förberedelse för nästa nyckels uppdateringscykel).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Hantera Azure SQL Database granskning

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

**PowerShell-cmdlets (inklusive WHERE-satsstöd för ytterligare filtrering):**

- [Skapa eller uppdatera granskningsprincip för databas (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Skapa eller uppdatera servergranskningsprincip (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Hämta databasgranskningsprincip (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Hämta servergranskningsprincip (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Ta bort databasgranskningsprincip (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Ta bort princip för servergranskning (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Ett skriptexempel finns i Konfigurera [granskning och hotidentifiering med Hjälp av PowerShell.](scripts/auditing-threat-detection-powershell-configure.md)

### <a name="using-rest-api"></a>Använda REST-API:et

**REST API**:

- [Skapa eller uppdatera granskningsprincip för databas](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Skapa eller uppdatera servergranskningsprincip](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Hämta databasgranskningsprincip](/rest/api/sql/database%20auditing%20settings/get)
- [Hämta servergranskningsprincip](/rest/api/sql/server%20auditing%20settings/get)

Utökad princip med WHERE-satsstöd för ytterligare filtrering:

- [Skapa eller uppdatera databasens *utökade* granskningsprincip](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Skapa eller uppdatera serverns *utökade* granskningsprincip](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Hämta utökad *granskningsprincip* för databasen](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Hämta serverns *utökade* granskningsprincip](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Använda Azure CLI

- [Hantera en server granskningsprincip](/cli/azure/sql/server/audit-policy)
- [Hantera en databas granskningsprincip](/cli/azure/sql/db/audit-policy)

### <a name="using-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar

Du kan hantera Azure SQL Database granskning med [Azure Resource Manager](../../azure-resource-manager/management/overview.md) mallar, som du ser i följande exempel:

- [Distribuera en Azure SQL Database med Granskning aktiverat för att skriva granskningsloggar till Azure Blob Storage-konto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Distribuera en Azure SQL Database med Granskning aktiverat för att skriva granskningsloggar till Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Distribuera en Azure SQL Database granskning aktiverat för att skriva granskningsloggar till Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> De länkade exemplen finns på en extern offentlig lagringsplats och tillhandahålls i "i sin mån" utan garanti och stöds inte under Microsofts supportprogram/-tjänster.
