---
title: Azure SQL-granskning för Azure SQL Database och Azure Synapse Analytics
description: Använd Azure SQL Database granskning för att spåra databas händelser till en Gransknings logg.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/03/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: 1c587705041b8fb3d37e02142fa10ae5a1d73f9f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173855"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Granskning för Azure SQL Database och Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Granskning för [Azure SQL Database](sql-database-paas-overview.md) och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) spårar databas händelser och skriver dem till en Gransknings logg i ditt Azure storage-konto, Log Analytics arbets yta eller Event Hubs.

Granskning gör även följande:

- Det hjälper dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insikter om i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

- Det främjar och underlättar uppfyllandet av efterlevnadsstandarder, även om det inte garanterar efterlevnad. Mer information om Azure-program som stöder standardkompatibilitet finns i [Azure Säkerhetscenter](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den mest aktuella listan med certifieringar för Azure SQL-kompatibilitet.

> [!NOTE]
> Information om granskning av Azure SQL-hanterad instans finns i följande artikel, [Kom igång med SQL-hanterad instans granskning](../managed-instance/auditing-configure.md).

## <a name="overview"></a><a id="overview"></a>Översikt

Du kan använda SQL Database granskning för att:

- **Behåll** en gransknings historik för markerade händelser. Du kan definiera kategorier med databasåtgärder som ska granskas.
- **Rapport** om databas aktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att snabbt komma igång med aktivitets- och händelserapportering.
- **Analysera** rapporter. Du kan hitta misstänkta händelser, ovanliga aktiviteter och trender.

> [!IMPORTANT]
> Granskning för Azure SQL Database och Azure-Synapse är optimerad för tillgänglighet och prestanda. Under mycket hög aktivitet, eller hög belastning i nätverket, Azure SQL Database och Azure-Synapse tillåta åtgärder att fortsätta och kan inte registrera vissa granskade händelser.

### <a name="auditing-limitations"></a>Gransknings begränsningar

- **Premium Storage** **stöds inte** för närvarande.
- Det finns för närvarande **inte stöd** för **hierarkiskt namn område** för **Azure Data Lake Storage Gen2 lagrings konto** .
- Det finns inte stöd för att aktivera granskning på en pausad **Azure-Synapse** . Du kan aktivera granskning genom att återuppta Azure Synapse.
- Granskning för **Azure SYNAPSE SQL-pooler** stöder **endast** standard gransknings åtgärds grupper.


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definiera server nivå kontra gransknings princip på databas nivå

En gransknings princip kan definieras för en viss databas eller som en standard [Server](logical-servers.md) princip i Azure (som är värd för SQL Database eller Azure-Synapse):

- En server princip gäller för alla befintliga och nyligen skapade databaser på servern.

- Om *Server granskning är aktiverat* *gäller det alltid för-databasen*. Databasen kommer att granskas, oavsett databas gransknings inställningar.

- När gransknings principen definieras på databas nivå till en Log Analytics arbets yta eller ett mål för Event Hub, kommer följande åtgärder inte att behålla gransknings principen på käll databas nivå:
    - [Databaskopia](database-copy.md)
    - [Återställning av tidpunkt](recovery-using-backups.md)
    - [Geo-replikering](active-geo-replication-overview.md) (sekundär databas kommer inte att ha granskning på databas nivå)

- Att aktivera granskning av databasen, förutom att aktivera den på-servern, Åsidosätt eller ändra *inte* inställningarna för Server granskning. Båda granskningarna kommer att finnas sida vid sida. Med andra ord granskas databasen två gånger parallellt. en gång av Server principen och en gång av databas principen.

   > [!NOTE]
   > Du bör undvika att aktivera granskning av både server granskning och databas-BLOB, om inte:
    >
    > - Du vill använda ett annat *lagrings konto*, en *kvarhållningsperiod* eller en *Log Analytics arbets yta* för en viss databas.
    > - Du vill granska händelse typer eller kategorier för en speciell databas som skiljer sig från resten av databaserna på servern. Du kan till exempel ha tabell infogningar som bara behöver granskas för en speciell databas.
   >
   > Annars rekommenderar vi att du bara aktiverar granskning på server nivå och lämnar granskning på databas nivå inaktive rad för alla databaser.

#### <a name="remarks"></a>Kommentarer

- Gransknings loggar skrivs för att **lägga till blobbar** i en Azure Blob Storage i din Azure-prenumeration
- Gransknings loggarna är i. xel-format och kan öppnas med hjälp av [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- Om du vill konfigurera ett oåterkalleligt logg Arkiv för gransknings händelser på Server-eller databas nivå följer du [instruktionerna som anges i Azure Storage](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes). Kontrol lera att du har valt **Tillåt ytterligare tillägg** när du konfigurerar den oföränderliga blob-lagringen.
- Du kan skriva gransknings loggar till ett Azure Storage konto bakom ett VNet eller en brand vägg. Mer information finns i [Skriv granskning till ett lagrings konto bakom VNet och brand vägg](audit-write-storage-account-behind-vnet-firewall.md).
- Mer information om logg formatet, hierarkin för lagringsmappen och namngivnings konventioner finns i [referensen för logg format för BLOB-granskning](./audit-log-format.md).
- Granskning är automatiskt aktiverat på [skrivskyddade repliker](read-scale-out.md). Mer information om hierarkin för lagrings-mappar, namngivnings konventioner och logg format finns i [SQL Database Gransknings logg format](audit-log-format.md).
- När du använder Azure AD-autentisering visas *inte* poster för misslyckade inloggningar i SQL-gransknings loggen. Om du vill visa gransknings poster för misslyckad inloggning måste du gå till [Azure Active Directory Portal](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)som loggar information om dessa händelser.
- Inloggningar dirigeras av gatewayen till den instans där databasen finns.  Vid en AAD-inloggning verifieras autentiseringsuppgifterna innan ett försök görs att logga in på den begärda databasen med aktuella användaren.  Om det uppstår ett fel går det inte att komma åt den begärda databasen, vilket innebär att det inte utförs någon granskning.  Om det gäller SQL-inloggningar verifieras autentiseringsuppgifterna för begärda data, så i så fall kan de granskas.  Lyckade inloggningar, som uppenbart når databasen, granskas i båda fallen.
- När du har konfigurerat dina gransknings inställningar kan du aktivera den nya funktionen för hot identifiering och konfigurera e-postmeddelanden för att få säkerhets aviseringar. När du använder hot identifiering får du proaktiva aviseringar om avvikande databas aktiviteter som kan innebära potentiella säkerhetshot. Mer information finns i [komma igång med hot identifiering](threat-detection-overview.md).

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Konfigurera granskning för servern

Standard gransknings principen omfattar alla åtgärder och följande uppsättning åtgärds grupper, som granskar alla frågor och lagrade procedurer som körs mot databasen, samt lyckade och misslyckade inloggningar:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
Du kan konfigurera granskning för olika typer av åtgärder och åtgärds grupper med hjälp av PowerShell, enligt beskrivningen i avsnittet [hantera SQL Database granskning med Azure PowerShell](#manage-auditing) .

Azure SQL Database och Azure Synapse audit lagrar 4000 tecken data för tecken fält i en gransknings post. När **instruktionen** eller **data_sensitivity_information** värden som returneras från en gransknings bar åtgärd innehåller fler än 4000 tecken kommer data utöver de första 4000 tecknen att **trunkeras och inte granskas**.
I följande avsnitt beskrivs konfigurationen av granskning med hjälp av Azure Portal.

  > [!NOTE]
  > - Det går inte att aktivera granskning på en pausad dedikerad SQL-pool. Om du vill aktivera granskning avbryter du den dedikerade SQL-poolen. Läs mer om [dedikerad SQL-pool](../..//synapse-analytics/sql/best-practices-sql-pool.md).
  > - När granskning har kon figurer ATS till en Log Analytics-arbetsyta eller till ett jämnt nav-mål via Azure Portal eller PowerShell-cmdleten skapas en [diagnostisk inställning](../../azure-monitor/essentials/diagnostic-settings.md) med "SQLSecurityAuditEvents"-kategorin aktive rad.

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Navigera till **granskning** under säkerhets rubriken i **SQL Database** eller **SQL Server** -fönstret.
3. Om du vill konfigurera en server gransknings princip kan du välja länken **Visa Server inställningar** på sidan databas granskning. Du kan sedan Visa eller ändra server gransknings inställningarna. Server gransknings principer gäller för alla befintliga och nyligen skapade databaser på den här servern.

    ![Skärm bild som visar länken Visa Server inställningar markerat på sidan databas granskning.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Om du föredrar att aktivera granskning på databas nivån växlar du **granskning** till **på**. Om Server granskning är aktive rad, finns den databas-konfigurerade granskningen sida vid sida med Server granskningen.

5. Du har flera alternativ för att konfigurera var gransknings loggar ska skrivas. Du kan skriva loggar till ett Azure Storage-konto till en Log Analytics arbets yta för användning genom att Azure Monitor loggar eller till Event Hub för användning med Event Hub. Du kan konfigurera valfri kombination av dessa alternativ och gransknings loggarna skrivs till var och en.
  
   ![lagrings alternativ](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>Granskning av Microsoft Support åtgärder

Granskning av Microsoft Support åtgärder för Azure SQL Server gör att du kan granska Microsofts support tekniker för att få åtkomst till servern under en support förfrågan. Användningen av den här funktionen, tillsammans med din granskning, möjliggör mer insyn i personalen och möjliggör avvikelse identifiering, trend visualisering och data förlust skydd.

Om du vill aktivera granskning av Microsoft Support åtgärder navigerar du till **granskning** under säkerhets rubriken i **Azure SQL Server** -fönstret och växlar **granskning av Microsofts support åtgärder** till **på**.

  > [!IMPORTANT]
  > Granskning av Microsofts support åtgärder stöder inte lagrings kontots destination. Om du vill aktivera funktionen måste en Log Analytics arbets yta eller ett mål för Event Hub konfigureras.

![Skärm bild av Microsoft Support åtgärder](./media/auditing-overview/support-operations.png)

Om du vill granska gransknings loggarna för Microsoft Support åtgärder på arbets ytan Log Analytics använder du följande fråga:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Granska till lagrings mål

Om du vill konfigurera att skriva gransknings loggar till ett lagrings konto väljer du **lagring** och öppna **lagrings information**. Välj det Azure Storage-konto där loggar ska sparas och välj sedan kvarhållningsperioden. Klicka sedan på **OK**. Loggar som är äldre än kvarhållningsperioden tas bort.

- Standardvärdet för kvarhållningsperiod är 0 (obegränsad kvarhållning). Du kan ändra det här värdet genom att flytta skjutreglaget för **kvarhållning (dagar)** i **lagrings inställningarna** när du konfigurerar lagrings kontot för granskning.
  - Om du ändrar kvarhållningsperioden från 0 (obegränsad kvarhållning) till ett annat värde, Observera att kvarhållning endast kommer att gälla för loggar som skrivits efter att kvarhållning har ändrats (loggar som skrivits under perioden när kvarhållning hade värdet obegränsat bevaras, även efter att kvarhållning har Aktiver ATS).

  ![storage account](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Granska till Log Analytics destination
  
Om du vill konfigurera att skriva gransknings loggar till en Log Analytics arbets yta väljer du **Log Analytics** och öppnar **Log Analytics information**. Välj eller skapa arbets ytan Log Analytics där loggarna ska skrivas och klicka sedan på **OK**.

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

Mer information om Azure Monitor Log Analytics arbets ytan finns i [utforma distributioner av Azure Monitor loggar](../../azure-monitor/logs/design-logs-deployment.md)
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Granska till Event Hub-målet

Om du vill konfigurera att skriva gransknings loggar till en händelsehubben väljer du **händelsehubben** och **information om** öppna händelsehubben. Välj den händelsehubben där loggar ska skrivas och klicka sedan på **OK**. Se till att händelsehubben är i samma region som din databas och server.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analysera gransknings loggar och rapporter

Om du väljer att skriva gransknings loggar till Azure Monitor loggar:

- Använd [Azure-portalen](https://portal.azure.com). Öppna relevant databas. Klicka på **Visa gransknings loggar** överst på databasens **gransknings** sida.

    ![Visa gransknings loggar](./media/auditing-overview/auditing-view-audit-logs.png)

- Sedan kan du visa loggarna på två sätt:

    Om du klickar på **Log Analytics** överst på sidan **gransknings poster** öppnas vyn loggar i Log Analytics arbets yta där du kan anpassa tidsintervallet och Sök frågan.

    ![Öppna i Log Analytics arbets ytan](./media/auditing-overview/auditing-log-analytics.png)

    Om du klickar på **Visa instrument panel** överst på sidan **gransknings poster** öppnas en instrument panel med information om gransknings loggar, där du kan öka detalj nivån i säkerhets insikter, komma åt känsliga data och mycket mer. Den här instrument panelen är utformad för att hjälpa dig att få säkerhets insikter om dina data.
    Du kan också anpassa tidsintervallet och Sök frågan.
    ![Visa Log Analytics instrument panel](media/auditing-overview/auditing-view-dashboard.png)

    ![Log Analytics instrument panel](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics säkerhets insikter](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- Du kan också få åtkomst till gransknings loggarna från Log Analytics bladet. Öppna arbets ytan Log Analytics och klicka på **loggar** under **Allmänt** . Du kan börja med en enkel fråga, till exempel: *Sök efter "SQLSecurityAuditEvents"* för att Visa gransknings loggarna.
    Härifrån kan du också använda [Azure Monitor loggar](../../azure-monitor/logs/log-query-overview.md)  för att köra avancerade sökningar på dina Gransknings logg data. Med Azure Monitor loggar får du operativa insikter i real tid med integrerad sökning och anpassade instrument paneler för att enkelt analysera miljon tals poster över alla dina arbets belastningar och servrar. Mer värdefull information om Azure Monitor loggar Sök språk och-kommandon finns i [Sök referens för Azure Monitor loggar](../../azure-monitor/logs/log-query-overview.md).

Om du väljer att skriva gransknings loggar till Händelsehubben:

- Om du vill använda gransknings loggar från Händelsehubben måste du konfigurera en data ström för att använda händelser och skriva dem till ett mål. Mer information finns i [Azure Event Hubs-dokumentationen](../index.yml).
- Gransknings loggar i Händelsehubben samlas in i bröd texten i [Apache Avro](https://avro.apache.org/) -händelser och lagras med JSON-FORMATERING med UTF-8-kodning. Om du vill läsa granskningsloggarna kan du använda [Avro-verktyg](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) eller liknande verktyg som kan hantera det här formatet.

Om du väljer att skriva gransknings loggar till ett Azure Storage-konto finns det flera metoder som du kan använda för att visa loggarna:

- Gransknings loggar sammanställs i det konto som du valde under installationen. Du kan utforska gransknings loggar med hjälp av ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/). I Azure Storage sparas gransknings loggar som en samling BLOB-filer i en behållare med namnet **sqldbauditlogs**. Mer information om hierarkin för lagrings-mappar, namngivnings konventioner och logg format finns i [SQL Database Gransknings logg format](./audit-log-format.md).

- Använd [Azure-portalen](https://portal.azure.com).  Öppna relevant databas. Klicka på **Visa gransknings loggar** överst i databasens **gransknings** sida.

    ![Skärm bild som visar knappen Visa gransknings loggar markerad på sidan databas granskning.](./media/auditing-overview/7_auditing_get_started_blob_view_audit_logs.png)

    **Gransknings poster** öppnas där du kan visa loggarna.

  - Du kan visa vissa datum genom att klicka på **filter** överst på sidan **gransknings poster** .
  - Du kan växla mellan gransknings poster som har skapats av *Server gransknings principen* och *databas gransknings principen* genom att växla **gransknings källa**.
  - Du kan bara visa SQL-injektering relaterade gransknings poster genom att markera kryss rutan  **Visa endast gransknings poster för SQL-injektering** .

       ![Skärm bild som visar alternativen för att Visa gransknings posterna.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- Använd system funktionen **sys.fn_get_audit_file** (T-SQL) för att returnera Gransknings logg data i tabell format. Mer information om hur du använder den här funktionen finns i [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Använda **sammanslagna gransknings filer** i SQL Server Management Studio (från och med SSMS 17):
    1. Från menyn SSMS väljer du **fil**  >  **Öppna**  >  **filer för sammanslagen granskning**.

        ![Skärm bild som visar meny alternativet Slå samman gransknings filer.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. Dialog rutan **Lägg till gransknings filer** öppnas. Välj ett av alternativen **Lägg** till för att välja om du vill slå samman gransknings filer från en lokal disk eller importera dem från Azure Storage. Du måste ange Azure Storage information och konto nyckeln.

    3. När du har lagt till alla filer som ska sammanfogas klickar du på **OK** för att slutföra sammanslagnings åtgärden.

    4. Den sammanslagna filen öppnas i SSMS, där du kan visa och analysera den, samt exportera den till en XEL-eller CSV-fil eller till en tabell.

- Använd Power BI. Du kan visa och analysera Gransknings logg data i Power BI. Mer information och åtkomst till en hämtnings bar mall finns i [analysera Gransknings logg data i Power BI](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895).
- Ladda ned loggfiler från din Azure Storage BLOB-behållare via portalen eller med ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/).
  - När du har laddat ned en loggfil lokalt, dubbelklickar du på filen för att öppna, Visa och analysera loggarna i SSMS.
  - Du kan också hämta flera filer samtidigt via Azure Storage Explorer. Det gör du genom att högerklicka på en angiven undermapp och välja **Spara som** för att spara i en lokal mapp.

- Ytterligare metoder:

  - När du har laddat ned flera filer eller en undermapp som innehåller loggfiler kan du sammanfoga dem lokalt enligt beskrivningen i anvisningarna för SSMS-sammanslagning av filer som beskrivs ovan.
  - Visa blobb gransknings loggar program mässigt: [fråga utökade Events-filer](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) med hjälp av PowerShell.

## <a name="production-practices"></a><a id="production-practices"></a>Produktions metoder


### <a name="auditing-geo-replicated-databases"></a>Granska geo-replikerade databaser

När du aktiverar granskning på den primära databasen med geo-replikerade databaser, kommer den sekundära databasen ha en identisk gransknings princip. Du kan också konfigurera granskning på den sekundära databasen genom att aktivera granskning på den **sekundära servern**, oberoende av den primära databasen.

- Server nivå (**rekommenderas**): aktivera granskning på både den **primära servern** och den **sekundära servern** – de primära och sekundära databaserna kommer att granskas oberoende av varandra baserat på deras respektive princip på server nivå.
- Databas nivå: granskning på databas nivå för sekundära databaser kan bara konfigureras från primära databas gransknings inställningar.
  - Granskning måste vara aktiverat på själva den *primära databasen*, inte på servern.
  - När granskning har Aktiver ATS på den primära databasen aktive ras den också på den sekundära databasen.

    > [!IMPORTANT]
    > Med granskning på databas nivå blir lagrings inställningarna för den sekundära databasen identiska med de som finns i den primära databasen, vilket orsakar en över-regional trafik. Vi rekommenderar att du bara aktiverar granskning på server nivå och låter granskning på databas nivå vara inaktive rad för alla databaser.

### <a name="storage-key-regeneration"></a>Återskapa lagrings nyckel

I produktion är det troligt att du uppdaterar dina lagrings nycklar med jämna mellanrum. När du skriver gransknings loggar till Azure Storage måste du spara om gransknings principen när du uppdaterar dina nycklar. Processen ser ut så här:

1. Öppna **lagrings information**. I rutan **lagrings åtkomst nyckel** väljer du **sekundär** och klickar på **OK**. Klicka sedan på **Spara** högst upp på sidan gransknings konfiguration.

    ![Skärm bild som visar processen för att välja en sekundär lagrings åtkomst nyckel.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. Gå till sidan lagrings konfiguration och återskapa den primära åtkomst nyckeln.

    ![Navigeringsfönster](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Gå tillbaka till sidan gransknings konfiguration, växla lagrings åtkomst nyckeln från sekundär till primär och klicka sedan på **OK**. Klicka sedan på **Spara** högst upp på sidan gransknings konfiguration.
4. Gå tillbaka till sidan lagrings konfiguration och återskapa den sekundära åtkomst nyckeln (som förberedelse för nästa nyckels uppdaterings cykel).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Hantera Azure SQL Database granskning

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

**PowerShell-cmdletar (inklusive WHERE-sats stöd för ytterligare filtrering)**:

- [Skapa eller uppdatera databas gransknings princip (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Skapa eller uppdatera Server gransknings princip (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Hämta databas gransknings princip (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Hämta Server gransknings princip (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Ta bort databas gransknings princip (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Ta bort Server gransknings princip (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Ett skript exempel finns i [Konfigurera granskning och hot identifiering med PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

### <a name="using-rest-api"></a>Använda REST-API:et

**REST API**:

- [Skapa eller uppdatera databas gransknings princip](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Skapa eller uppdatera Server gransknings princip](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Hämta databas gransknings princip](/rest/api/sql/database%20auditing%20settings/get)
- [Hämta Server gransknings princip](/rest/api/sql/server%20auditing%20settings/get)

Utökad princip med stöd för WHERE-sats för ytterligare filtrering:

- [Skapa eller uppdatera *utökad* gransknings princip för databasen](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Skapa eller uppdatera *utökad* gransknings princip för Server](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Hämta *utökad* gransknings princip för databas](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Hämta *utökad* gransknings princip för Server](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Använda Azure CLI

- [Hantera en servers gransknings princip](/cli/azure/sql/server/audit-policy)
- [Hantera en Databass gransknings princip](/cli/azure/sql/db/audit-policy)

### <a name="using-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar

Du kan hantera Azure SQL Database granskning med [Azure Resource Manager](../../azure-resource-manager/management/overview.md) mallar, som du ser i följande exempel:

- [Distribuera ett Azure SQL Database med granskning aktiverat för att skriva gransknings loggar till Azure Blob Storage-konto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Distribuera ett Azure SQL Database med granskning aktiverat för att skriva gransknings loggar till Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Distribuera ett Azure SQL Database med granskning aktiverat för att skriva gransknings loggar till Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> De länkade exemplen finns på ett externt offentligt lager och tillhandahålls i befintligt skick, utan garanti, och stöds inte i Microsoft Support program/-tjänster.
