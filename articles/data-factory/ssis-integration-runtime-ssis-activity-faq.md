---
title: Felsöka paket körning i SSIS integration runtime
description: Den här artikeln innehåller fel söknings vägledning för körning av SSIS-paket i SSIS integration runtime
ms.service: data-factory
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 2bc56d39de392c9e4c20c25b554e3bdeea048bfb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361884"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Felsöka paket körning i SSIS integration runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln innehåller de vanligaste fel som kan uppstå när du kör SQL Server Integration Services-paket (SSIS) i SSIS integration Runtime. Det beskriver möjliga orsaker och åtgärder för att lösa felen.

## <a name="where-to-find-logs-for-troubleshooting"></a>Här hittar du loggar för fel sökning

Använd Azure Data Factory Portal för att kontrol lera utdata från körnings aktiviteten för SSIS-paketet. I utdata ingår körnings resultat, fel meddelanden och åtgärds-ID. Mer information finns i [övervaka pipelinen](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Använd SSIS-katalogen (SSISDB) för att kontrol lera informations loggarna för körningen. Mer information finns i [övervaka paket som körs och andra åtgärder](/sql/integration-services/performance/monitor-running-packages-and-other-operations).

## <a name="common-errors-causes-and-solutions"></a>Vanliga fel, orsaker och lösningar

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Fel meddelande: "anslutningens tids gräns har upphört att gälla" eller "tjänsten har påträffat ett fel vid bearbetning av begäran. Försök igen.”

Här är möjliga orsaker och rekommenderade åtgärder:
* Data källan eller målet är överbelastat. Kontrol lera belastningen på data källan eller målet och se om det finns tillräckligt med kapacitet. Om du t. ex. använde Azure SQL Database kan du skala upp om databasen troligen är tids gräns.
* Nätverket mellan SSIS-integrerings körningen och data källan eller målet är instabilt, särskilt när anslutningen är mellan olika regioner eller mellan lokala platser och Azure. Använd mönstret för återförsök i SSIS-paketet genom att följa dessa steg:
  * Se till att dina SSIS-paket kan köras igen vid fel utan sido effekter (till exempel data förlust eller dataduplicering).
  * Konfigurera **återförsök** **och återförsöksintervall** för aktiviteten **kör SSIS-paket** på fliken **Allmänt** . ![ Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * För en ADO.NET och OLE DB käll-eller mål komponent anger du **ConnectRetryCount** och **ConnectRetryInterval** i anslutnings hanteraren i SSIS-paketet eller SSIS-aktiviteten.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Fel meddelande: "ADO NET source kunde inte hämta anslutningen"... "" med "ett nätverksrelaterade eller instans-relaterat fel uppstod när en anslutning upprättades till SQL Server. Det gick inte att hitta servern eller så var den inte tillgänglig. "

Det här problemet innebär vanligtvis att datakällan eller målet inte kan nås från SSIS Integration Runtime. Anledningen kan variera. Försök med följande åtgärder:
* Kontrol lera att du har överför data källan eller mål namnet/IP-adressen korrekt.
* Kontrol lera att brand väggen är korrekt inställd.
* Kontrol lera att det virtuella nätverket är korrekt konfigurerat om data källan eller målet är lokalt:
  * Du kan kontrol lera om problemet beror på konfigurationen av virtuella nätverk genom att tillhandahålla en virtuell Azure-dator i samma virtuella nätverk. Kontrol lera sedan om data källan eller målet kan nås från den virtuella Azure-datorn.
  * Du hittar mer information om hur du använder ett virtuellt nätverk med en SSIS integration runtime i delta i en [Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Fel meddelande: "ADO NET source kunde inte hämta anslutningen"... "" med "Det gick inte att skapa en hanterad anslutnings hanterare".

Den potentiella orsaken är att ADO.NET-providern som används i paketet inte är installerad i SSIS integration Runtime. Du kan installera providern med hjälp av en anpassad installation. Du hittar mer information om anpassade inställningar i [Anpassa installations programmet för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Fel meddelande: "anslutningen"... hittades inte "

Ett känt problem i äldre versioner av SQL Server Management Studio (SSMS) kan orsaka det här felet. Om paketet innehåller en anpassad komponent (till exempel SSIS Azure Feature Pack eller partnerkomponenter) som inte är installerad på den dator där SSMS används för att utföra distributionen, tar SSMS bort komponenten och orsakar felet. Uppgradera [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) till den senaste versionen där problemet är korrigerat.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Fel meddelande: "SSIS utförar avslutning Code:-1073741819".

* Möjlig orsak och rekommenderad åtgärd:
  * Det här felet kan bero på begränsningen för Excel-källa och-mål när flera Excel-källor eller-mål körs parallellt i flera trådar. Du kan lösa den här begränsningen genom att ändra dina Excel-komponenter så att de körs i följd, eller separera dem till olika paket och utlösa genom att köra paket uppgift med egenskapen ExecuteOutOfProcess inställd på sant.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Fel meddelande: "det finns inte tillräckligt med utrymme på disken"

Det här felet innebär att den lokala disken används i SSIS-noden för integration Runtime. Kontrol lera om paketet eller den anpassade installationen tar mycket disk utrymme:
* Om disken används av ditt paket frigörs den när paket körningen har slutförts.
* Om disken används av den anpassade installationen måste du stoppa SSIS integration runtime, ändra skriptet och starta integrerings körningen igen. Hela Azure Blob-behållaren som du har angett för anpassad installation kopieras till SSIS-noden för integration runtime, så kontrol lera om det finns onödigt innehåll under behållaren.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Fel meddelande: "Det gick inte att hämta resursen från huvud servern. Microsoft. SqlServer. IntegrationServices. Scale. ScaleoutContract. Common. MasterResponseFailedException: code: 300004. Beskrivning: det gick inte att läsa in filen "* * *". "

* Möjlig orsak och rekommenderad åtgärd:
  * Om SSIS-aktiviteten kör ett paket från fil systemet (paketfil eller projekt filen) uppstår det här felet om projektet, paketet eller konfigurations filen inte är tillgängliga med de autentiseringsuppgifter för paket åtkomst som du angav i SSIS-aktiviteten
    * Om du använder Azure-fil:
      * Fil Sök vägen ska börja med \\ \\ \<storage account name\> . File.Core.Windows.net\\\<file share path\>
      * Domänen ska vara "Azure"
      * Användar namnet ska vara \<storage account name\>
      * Lösen ordet ska vara \<storage access key\>
    * Om du använder en lokal fil kontrollerar du om VNet, paket åtkomst behörighet och behörighet har kon figurer ATS korrekt så att din Azure-SSIS integration runtime kan komma åt din lokala fil resurs

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Fel meddelande: "fil namnet"... angavs i anslutningen var inte giltigt.

* Möjlig orsak och rekommenderad åtgärd:
  * Ett ogiltigt fil namn har angetts
  * Kontrol lera att du använder FQDN (fullständigt kvalificerat domän namn) i stället för kort tid i anslutnings hanteraren

### <a name="error-message-cannot-open-file-"></a>Fel meddelande: "det går inte att öppna filen"... ""

Det här felet uppstår när paket körningen inte kan hitta en fil på den lokala disken i SSIS integration Runtime. Försök med följande åtgärder:
* Använd inte den absoluta sökvägen i paketet som körs i SSIS integration Runtime. Använd den aktuella körnings arbets katalogen (.) eller Temp-mappen (% TEMP%) i stället.
* Om du behöver spara några filer på SSIS-noderna för integration runtime förbereder du filerna enligt beskrivningen i [Anpassa installationen](how-to-configure-azure-ssis-ir-custom-setup.md). Alla filer i arbets katalogen kommer att rensas när körningen har slutförts.
* Använd Azure Files i stället för att lagra filen i SSIS integration runtime-noden. Mer information finns i [använda Azure-filresurser](/sql/integration-services/lift-shift/ssis-azure-files-file-shares#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Fel meddelande: "databasen SSISDB har nått sin storleks kvot"

En möjlig orsak är att SSISDB-databasen som skapades i Azure SQL Database eller i SQL-hanterad instans har nått sin kvot. Försök med följande åtgärder:
* Överväg att öka antalet DTU:er för din databas. Du hittar mer information i [SQL Database gränser för en logisk server](../azure-sql/database/resource-limits-logical-server.md).
* Kontrollera om ditt paket skulle generera många loggar. I så fall kan du konfigurera ett elastiskt jobb så att dessa loggar rensas. Mer information finns i [Rensa SSISDB-loggar med Azure Elastic Database-jobb](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Fel meddelande: "gränsen för begäran för databasen är... och har nåtts. "

Om många paket körs parallellt i SSIS-integrerings körningen kan det här felet inträffa eftersom SSISDB har nått sin gräns för begäran. Överväg att öka DTC-SSISDB för att lösa problemet. Du hittar mer information i [SQL Database gränser för en logisk server](../azure-sql/database/resource-limits-logical-server.md).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Fel meddelande: "SSIS-åtgärden misslyckades med oväntad åtgärds status:..."

Felet orsakas oftast av ett tillfälligt problem, så försök att köra paket körningen igen. Använd mönstret för återförsök i SSIS-paketet genom att följa dessa steg:

* Se till att dina SSIS-paket kan köras igen vid fel utan sido effekter (till exempel data förlust eller dataduplicering).
* Konfigurera **återförsök** **och återförsöksintervall** för aktiviteten **kör SSIS-paket** på fliken **Allmänt** . ![ Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* För en ADO.NET och OLE DB käll-eller mål komponent anger du **ConnectRetryCount** och **ConnectRetryInterval** i anslutnings hanteraren i SSIS-paketet eller SSIS-aktiviteten.

### <a name="error-message-there-is-no-active-worker"></a>Fel meddelande: "det finns ingen aktiv arbets rutin".

Det här felet innebär vanligt vis att SSIS integration Runtime har en felaktig status. Kontrol lera Azure Portal för status och detaljerade fel. Mer information finns i [Azure-SSIS integration runtime](./monitor-integration-runtime.md#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Fel meddelande: "det går inte att uppgradera integrerings körningen och upphör att fungera, eftersom det inte går att komma åt Azure Blob-behållaren som du angav för anpassad installation."

Felet uppstår när SSIS-integrerings körningen inte kan komma åt lagrings utrymmet som kon figurer ATS för anpassad installation. Kontrol lera om den angivna URL: en för signaturen för delad åtkomst (SAS) är giltig och inte har gått ut.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Fel meddelande: "Microsoft OLE DB Provider för Analysis Services. ' HRESULT: 0x80004005 visas Beskrivning: ' COM-fel: COM-fel: mscorlib; Ett undantag har utlösts av målet för ett anrop "

En möjlig orsak är att användar namnet eller lösen ordet med Azure AD Multi-Factor Authentication aktiverat har kon figurer ATS för Azure Analysis Services autentisering. Den här autentiseringen stöds inte i integrerings körningen för SSIS. Försök att använda ett huvud namn för tjänsten för Azure Analysis Services autentisering:

1. Förbered ett huvud namn för tjänsten enligt beskrivningen i [Automation med tjänstens huvud namn](../analysis-services/analysis-services-service-principal.md).
2. I anslutnings hanteraren konfigurerar du **Använd ett särskilt användar namn och lösen ord**: ange **AppID** som användar namn och **clientSecret** som lösen ord.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Fel meddelande: "ADONET-källan kunde inte hämta anslutningen {GUID} med följande fel meddelande: inloggningen misslyckades för användarens NT AUTHORITY\ANONYMOUS-inloggning" "när en hanterad identitet används

Se till att du inte konfigurerar autentiseringsmetoden för anslutnings hanteraren som **Active Directory** lösenordsautentisering när parametern *ConnectUsingManagedIdentity* är **True**. Du kan konfigurera den som **SQL-autentisering** i stället, vilket ignoreras om *ConnectUsingManagedIdentity* har angetts.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Fel meddelande: "0xC020801F vid..., OData-Källa [...]: det går inte att hämta en hanterad anslutning från kör tids anslutnings hanteraren"

En möjlig orsak är att Transport Layer Security (TLS) inte är aktiverat i SSIS integration runtime som krävs av din OData-källa. Du kan aktivera TLS i SSIS integration runtime genom att använda anpassa installationen. Mer information finns på [det går inte att ansluta Project Online OData från SSIS](/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) och [Anpassa installationen för integrerings körningen för Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Fel meddelande: "begär mellanlagrings uppgift med åtgärds-GUID... misslyckades eftersom fel: det gick inte att skicka mellanlagrings åtgärd med fel meddelande: Microsoft. SqlServer. IntegrationServices. AisAgentCore. AisAgentException: det gick inte att läsa in dataproxy.

Kontrol lera att Azure-SSIS integration Runtime har kon figurer ATS med Self-Hosted integration Runtime. Mer information finns i [konfigurera Self-Hosted IR som en proxy för Azure-SSIS IR i ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Fel meddelande: "status för mellanlagring av aktivitet: misslyckades. Fel vid mellanlagring: felkod: 2010, ErrorMessage: den egna värd Integration Runtime... är offline "

Kontrol lera att Self-Hosted integration Runtime har installerats och startats. Mer information finns i [skapa och konfigurera en integration runtime med egen värd](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Fel meddelande: "mellanlagrings åtgärds fel: felkod: 2906, ErrorMessage: det gick inte att köra paketet., utdata: {" OperationErrorMessages ":" fel: den begärda OLE DB providern... är inte registrerad. Om 64-bitars driv rutinen inte är installerad kör du paketet i 32-bitars läge... "

Kontrol lera att motsvarande provider som används av dina OLE DB-kopplingar i paketet är installerade på Self-Hosted integration runtime-datorn korrekt. Mer information finns i [konfigurera Self-Hosted IR som proxy för Azure-SSIS IR i ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Fel meddelande: "mellanlagrings åtgärds fel: felkod: 2906, ErrorMessage: det gick inte att köra paketet., utdata: {" OperationErrorMessages ":" fel: system. IO. FileLoadException: det gick inte att läsa in filen eller sammansättningen "Microsoft. WindowsAzure. Storage, version =..., Culture = neutral, PublicKeyToken = 31bf3856ad364e35" eller något av dess beroenden. Den Funna sammansättningens manifest definition matchar inte sammansättnings referensen. ..."

En möjlig orsak är att Self-Hosted integration runtime inte har installerats eller uppgraderats korrekt. Föreslå att ladda ned och installera om den senaste integrerings körningen med egen värd. Mer information finns i [skapa och konfigurera en integration runtime med egen värd](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Fel meddelande: "en anslutning krävs när metadata begärs. Om du arbetar offline avmarkerar du arbeta offline på SSIS-menyn för att aktivera anslutningen.

* Möjlig orsak och rekommenderad åtgärd:
  * Om det också finns ett varnings meddelande om att komponenten inte stöder användning av anslutnings hanteraren med värdet true i ConnectByProxy i körnings loggen, innebär det att en anslutnings hanterare används på en komponent som inte har stöd för "ConnectByProxy" än. De komponenter som stöds finns i [konfigurera Self-Hosted IR som proxy för Azure-SSIS IR i ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * Du kan hitta körnings loggen i [SSMS-rapporten](/sql/integration-services/performance/monitor-running-packages-and-other-operations#reports) eller i den loggfil som du angav i SSIS-paketets körnings aktivitet.
  * vNet kan också användas för att komma åt lokala data som ett alternativ. Mer information finns i delta i en [Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Fel meddelande: "status för mellanlagring av aktivitet: misslyckades. Fel vid mellanlagring: felkod: 2906, ErrorMessage: det gick inte att köra paketet., utdata: {"OperationErrorMessages": "SSIS utförar slutkod:-1. \ n", "LogLocation": "... \\ SSISTelemetry \\ ExecutionLog \\ ... "," effectiveIntegrationRuntime ":"... "," executionDuration ":...," durationInQueue ": {" integrationRuntimeQueue ":...}}"

Kontrol lera att Visual C++ runtime är installerat på Self-Hosted integration runtime Machine. Mer information finns i [konfigurera Self-Hosted IR som proxy för Azure-SSIS IR i ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Flera paket körningar utlöses oväntade

* Möjlig orsak och rekommenderad åtgärd:
  * ADF-lagrad procedur aktivitet eller söknings aktivitet används för att utlösa körning av SSIS-paket. T-SQL-kommandot kan nå ett tillfälligt problem och utlösa omkörningen vilket skulle orsaka flera paket körningar.
  * Använd ExecuteSSISPackage-aktivitet i stället för att se till att paket körningen inte körs igen om inte antalet nya försök i aktiviteten. Information finns på [https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](./how-to-invoke-ssis-package-ssis-activity.md)
  * Förfina t-SQL-kommandot för att kunna köra igen genom att kontrol lera om en körning redan har utlösts

### <a name="package-execution-takes-too-long"></a>Paket körningen tar för lång tid

Här är möjliga orsaker och rekommenderade åtgärder:

* För många paket körningar har schemalagts i SSIS integration Runtime. Alla dessa körningar väntar i en kö för sin tur.
  * Fastställ Max värdet genom att använda den här formeln:

    Maximalt antal parallella körningar per IR = antal noder * maximal parallell körning per nod
  * Information om hur du ställer in antal noder och maximal parallell körning per nod finns i [skapa en Azure-SSIS integration runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Integrerings körningen för SSIS har stoppats eller är i fel tillstånd. Information om hur du kontrollerar SSIS för integration runtime finns i [Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Vi rekommenderar också att du anger en tids gräns på fliken **Allmänt** : ![ Ange egenskaper på fliken Allmänt ](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) .

### <a name="poor-performance-in-package-execution"></a>Dåliga prestanda vid paket körning

Försök med följande åtgärder:

* Se till att integration runtime för SSIS finns i samma region som data källan och målet.

* Ange loggnings nivå för paket körning till **prestanda** för att samla in varaktighets information för varje komponent i körningen. Mer information finns i [loggning av integrerings tjänster (SSIS)](/sql/integration-services/performance/integration-services-ssis-logging).

* Kontrol lera prestanda för IR-nod i Azure Portal:
  * Information om hur du övervakar SSIS integration runtime finns i [Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Du hittar processor-/minnes historik för SSIS integration runtime genom att visa måtten för data fabriken i Azure Portal.
    ![Övervaka mått för integrerings körningen i SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)