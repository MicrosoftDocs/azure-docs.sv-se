---
title: Felsöka Azure Data Factory-anslutningsprogram
description: Lär dig hur du felsöker anslutnings problem i Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/08/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 574c4967c1e45ce1ae2be92d8648d654322e2244
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727829"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Felsöka Azure Data Factory-anslutningsprogram

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln visar vanliga sätt att felsöka problem med Azure Data Factory-anslutningar.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Felkod: AzureBlobOperationFailed

- **Meddelande**: "BLOB-åtgärden misslyckades. ContainerName:% containerName;, sökväg:% Path;. "

- **Orsak**: ett problem med Blob Storage åtgärden.

- **Rekommendation**: information om felet finns i [Blob Storage fel koder](/rest/api/storageservices/blob-service-error-codes). Kontakta Blob Storage-teamet om du behöver ytterligare hjälp.


### <a name="invalid-property-during-copy-activity"></a>Ogiltig egenskap vid kopierings aktivitet

- **Meddelande**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Orsak**: den typ som definierats i data uppsättningen stämmer inte med käll-eller mottagar typen som har definierats i kopierings aktiviteten.

- **Lösning**: redigera data uppsättningen eller pipeline-JSON-definitionen för att göra typerna konsekventa och kör sedan distributionen igen.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Fel meddelande: den begärda storleken är för stor

- **Symptom**: när du kopierar data till Azure Cosmos dB med en standard storlek för Skriv batch får du följande fel meddelande: `Request size is too large.`

- **Orsak**: Azure Cosmos DB begränsar storleken på en enskild begäran till 2 MB. Formeln är *en begär ande storlek = enskild dokument storlek \* Skriv batchstorleken*. Om din dokument storlek är stor resulterar standard beteendet i en begär ande storlek som är för stor. Du kan finjustera Skriv batchstorleken.

- **Lösning**: minska värdet för *Skriv Journal storlek* i kopians aktivitets mottagare (standardvärdet är 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Fel meddelande: överträdelse av unikhetsvillkor för unikt index

- **Symptom**: när du kopierar data till Azure Cosmos DB får du följande fel meddelande:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Orsak**: det finns två möjliga orsaker:

    - Orsak 1: om du använder **insert** som Skriv beteende innebär det här felet att dina källdata innehåller rader eller objekt med samma ID.
    - Orsak 2: om du använder **upsert** som Skriv beteende och anger en annan unik nyckel till behållaren, innebär det här felet att dina källdata innehåller rader eller objekt med olika ID: n men samma värde för den definierade unika nyckeln.

- **Lösning**: 

    - Ange **upsert** som Skriv-beteende för orsak 1.
    - För orsak 2, se till att varje dokument har ett annat värde för den definierade unika nyckeln.

### <a name="error-message-request-rate-is-large"></a>Fel meddelande: den begärda frekvensen är stor

- **Symptom**: när du kopierar data till Azure Cosmos DB får du följande fel meddelande:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Orsak**: antalet använda begär ande enheter (ru: er) är större än det tillgängliga ru: er som kon figurer ats i Azure Cosmos dB. Information om hur Azure Cosmos DB beräknar ru: er finns [i enheter för programbegäran i Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Lösning**: prova någon av följande två lösningar:

    - Öka ru: er-talet för *container* till ett större värde i Azure Cosmos dB. Den här lösningen förbättrar prestanda för kopierings aktiviteten, men den kommer att ådra sig mer kostnad i Azure Cosmos DB. 
    - Minska *writeBatchSize* till ett lägre värde, till exempel 1000, och minska *parallelCopies* till ett lägre värde, t. ex. 1. Den här lösningen minskar prestanda för kopierings körning, men kostar inte att Azure Cosmos DB.

### <a name="columns-missing-in-column-mapping"></a>Kolumner saknas i kolumn mappning

- **Symptom**: när du importerar ett schema för Azure Cosmos dB för kolumn mappning saknas vissa kolumner. 

- **Orsak**: Data Factory härleder schemat från de första 10 Azure Cosmos DB dokumenten. Om vissa dokument kolumner eller egenskaper inte innehåller värden identifieras inte schemat av Data Factory och visas därför inte.

- **Lösning**: du kan finjustera frågan så som visas i följande kod för att tvinga kolumnvärdena att visas i resultat uppsättningen med tomma värden. Anta att kolumnen *omöjlig* saknas i de första 10 dokumenten. Alternativt kan du lägga till kolumnen manuellt för mappning.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Fel meddelande: GuidRepresentation för läsaren är CSharpLegacy

- **Symptom**: när du kopierar data från Azure Cosmos DB MongoAPI eller MongoDB med fältet UUID (Universal Unique Identifier) får du följande fel meddelande:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Orsak**: det finns två sätt att representera UUID i binär JSON (bson): UuidStardard och UuidLegacy. Som standard används UuidLegacy för att läsa data. Du får ett fel meddelande om dina UUID-data i MongoDB är UuidStandard.

- **Lösning**: Lägg till alternativet *uuidRepresentation = standard* i anslutnings strängen MongoDB. Mer information finns i [anslutnings strängen för MongoDB](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Felkod: CosmosDbSqlApiOperationFailed

- **Meddelande**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Orsak**: ett problem med CosmosDbSqlApi-åtgärden.

- **Rekommendation**: information om felet finns i [Azure Cosmos DB hjälp dokument](../cosmos-db/troubleshoot-dot-net-sdk.md). Kontakta Azure Cosmos DB-teamet om du behöver ytterligare hjälp.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Fel meddelande: den underliggande anslutningen stängdes: det gick inte att upprätta en förtroende relation för den säkra SSL/TLS-kanalen.

- **Symptom**: kopierings aktiviteten Miss lyckas med följande fel: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Orsak**: certifikat valideringen misslyckades under TLS-handskakningen.

- **Lösning**: Använd den mellanlagrade kopian för att hoppa över verifieringen av Transport Layer Security (TLS) för Azure Data Lake Storage gen1. Du måste återskapa det här problemet och samla in Network Monitor-spårningen (Netmon) och sedan engagera nätverks teamet för att kontrol lera den lokala nätverks konfigurationen.

    ![Diagram över Azure Data Lake Storage Gen1 anslutningar för fel söknings problem.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Fel meddelande: fjärrservern returnerade ett fel: (403) ej tillåtet

- **Symptom**: kopierings aktiviteten fungerar inte med följande fel: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Orsak**: en möjlig orsak är att tjänstens huvud namn eller hanterade identitet som du använder inte har behörighet att komma åt vissa mappar eller filer.

- **Lösning**: bevilja lämpliga behörigheter till alla mappar och undermappar som du behöver kopiera. Mer information finns i [Kopiera data till eller från Azure Data Lake Storage gen1 med Azure Data Factory](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Fel meddelande: det gick inte att hämta åtkomsttoken med hjälp av tjänstens huvud namn. ADAL-fel: service_unavailable

- **Symptom**: kopierings aktiviteten Miss lyckas med följande fel:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Orsak**: när den server (STS) som ägs av Azure Active Directory inte är tillgänglig, innebär det att den är upptagen för att hantera begär Anden och returnerar HTTP-fel 503. 

- **Lösning**: kör kopierings aktiviteten igen om några minuter.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Felkod: ADLSGen2OperationFailed

- **Meddelande**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Orsaker och rekommendationer**: olika orsaker kan leda till det här felet. Kontrol lera nedan för möjlig orsaks analys och relaterad rekommendation.

  | Orsaka analys                                               | Rekommendation                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Om Azure Data Lake Storage Gen2 genererar fel som indikerar att en åtgärd misslyckades.| Kontrol lera det detaljerade fel meddelandet som har utlösts av Azure Data Lake Storage Gen2. Försök igen om felet är ett tillfälligt fel. Kontakta Azure Storage support för ytterligare hjälp och ange ID för begäran i fel meddelande. |
  | Om fel meddelandet innehåller strängen "förbjuden" kanske tjänstens huvud namn eller hanterade identitet som du använder inte har tillräcklig behörighet för att komma åt Azure Data Lake Storage Gen2. | Information om hur du felsöker det här felet finns [i kopiera och transformera data i Azure Data Lake Storage Gen2 med hjälp av Azure Data Factory](./connector-azure-data-lake-storage.md#service-principal-authentication). |
  | Om fel meddelandet innehåller strängen "InternalServerError" returneras felet av Azure Data Lake Storage Gen2. | Felet kan ha orsakats av ett tillfälligt fel. Om den är det kan du försöka att utföra åtgärden igen. Kontakta Azure Storage-supporten och ange ID: t för begäran från fel meddelandet om problemet kvarstår. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Begäran om att Azure Data Lake Storage Gen2 kontot orsakade ett tids gräns fel

- **Meddelande**: 
  * Felkod = `UserErrorFailedBlobFSOperation`
  * Fel meddelande = `BlobFS operation failed for: A task was canceled.`

- **Orsak**: problemet orsakas av Azure Data Lake Storage Gen2 Sink-tidsgräns, som vanligt vis inträffar på IR-datorn (Self-hosted integration Runtime).

- **Rekommendation**: 

    - Placera din egen IR-dator och mål Azure Data Lake Storage Gen2 konto i samma region, om möjligt. Detta kan hjälpa till att undvika slumpmässiga tids gräns fel och få bättre prestanda.

    - Kontrol lera om det finns en särskild nätverks inställning, till exempel ExpressRoute, och se till att nätverket har tillräckligt med bandbredd. Vi rekommenderar att du sänker inställningen för IR-arbetsjobb med egen värd när den totala bandbredden är låg. På så sätt kan du undvika nätverks resurs konkurrens mellan flera samtidiga jobb.

    - Om fil storleken är måttlig eller liten ska du använda en mindre block storlek för en binär kopia för att minimera sådana tids gräns fel. Mer information finns i [Blob Storage spärra block](/rest/api/storageservices/put-block).

       Om du vill ange den anpassade block storleken redigerar du egenskapen i din JSON-filredigerare som du ser här:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Azure Files lagring

### <a name="error-code-azurefileoperationfailed"></a>Felkod: AzureFileOperationFailed

- **Meddelande**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Orsak**: ett problem med Azure Files lagrings åtgärden.

- **Rekommendation**: information om felet finns i hjälpen för [Azure Files](/rest/api/storageservices/file-service-error-codes). Kontakta Azure Files-teamet om du behöver ytterligare hjälp.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, Azure SQL Database och SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Felkod: SqlFailedToConnect

- **Meddelande**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Orsaker och rekommendationer**: olika orsaker kan leda till det här felet. Kontrol lera nedan för möjlig orsaks analys och relaterad rekommendation.

    | Orsaka analys                                               | Rekommendation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | För Azure SQL, om fel meddelandet innehåller strängen "SqlErrorNumber = 47073", innebär det att offentlig nätverks åtkomst nekas i anslutnings inställningen. | I Azure SQL-brandväggen anger du alternativet **neka offentligt nätverks åtkomst** till *Nej*. Mer information finns i [Inställningar för Azure SQL-anslutning](../azure-sql/database/connectivity-settings.md#deny-public-network-access). |
    | Om fel meddelandet innehåller en SQL-felkod som "SqlErrorNumber = [ErrorCode]" i Azure SQL kan du läsa mer i fel söknings guiden för Azure SQL. | En rekommendation finns i [Felsöka anslutnings problem och andra fel med Azure SQL Database och Azure SQL-hanterad instans](../azure-sql/database/troubleshoot-common-errors-issues.md). |
    | Kontrol lera om port 1433 finns i listan över tillåtna brand Väggs listor. | Mer information finns i [portar som används av SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Om fel meddelandet innehåller strängen "SqlException" SQL Database felet indikerar att en viss åtgärd misslyckades. | Om du vill ha mer information kan du söka i SQL-felkoden i [databas motor fel](/sql/relational-databases/errors-events/database-engine-events-and-errors). Kontakta Azure SQL-supporten om du vill ha mer hjälp. |
    | Om detta är ett tillfälligt problem (till exempel en instabil nätverks anslutning) lägger du till försök igen i aktivitets principen för att minimera. | Mer information finns i [pipelines och aktiviteter i Azure Data Factory](./concepts-pipelines-activities.md#activity-policy). |
    | Om fel meddelandet innehåller strängen "client med IP-adress"... " är inte tillåten för att komma åt servern och du försöker ansluta till Azure SQL Database, felet orsakas vanligt vis av ett problem med Azure SQL Database brand väggen. | I Azure SQL Server Firewall-konfigurationen aktiverar du alternativet **Tillåt Azure-tjänster och-resurser för att komma åt den här servern** . Mer information finns i [Azure SQL Database och Azure SYNAPSE IP Firewall Rules](../azure-sql/database/firewall-configure.md). |
    
### <a name="error-code-sqloperationfailed"></a>Felkod: SqlOperationFailed

- **Meddelande**: `A database operation failed. Please search error to get more details.`

- **Orsaker och rekommendationer**: olika orsaker kan leda till det här felet. Kontrol lera nedan för möjlig orsaks analys och relaterad rekommendation.

    | Orsaka analys                                               | Rekommendation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Om fel meddelandet innehåller strängen "SqlException", genererar SQL Database ett fel som indikerar att en viss åtgärd misslyckades. | Om SQL-felet inte är klart försöker du ändra databasen till den senaste kompatibilitetsnivån "150". Den kan resultera i den senaste versionen av SQL-fel. Mer information finns i [dokumentationen](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> Om du vill ha mer information om fel sökning av SQL-problem kan du söka i SQL-felkoden i [databas motor fel](/sql/relational-databases/errors-events/database-engine-events-and-errors). Kontakta Azure SQL-supporten om du vill ha mer hjälp. |
    | Om fel meddelandet innehåller strängen "PdwManagedToNativeInteropException", orsakas det vanligt vis av ett matchnings fel mellan kolumn storlekarna källa och mottagare. | Kontrol lera storleken på kolumnerna källa och mottagare. Kontakta Azure SQL-supporten om du vill ha mer hjälp. |
    | Om fel meddelandet innehåller strängen "InvalidOperationException", orsakas det vanligt vis av ogiltiga indata. | För att identifiera vilken rad som har påträffat problemet aktiverar du fel tolerans funktionen på kopierings aktiviteten, som kan dirigera om problematiska rader till lagringen för ytterligare undersökning. Mer information finns i [fel tolerans för kopierings aktivitet i Azure Data Factory](./copy-activity-fault-tolerance.md). |


### <a name="error-code-sqlunauthorizedaccess"></a>Felkod: SqlUnauthorizedAccess

- **Meddelande**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Orsak**: autentiseringsuppgifterna är felaktiga eller så har inloggnings kontot inte åtkomst till SQL-databasen.

- **Rekommendation**: kontrol lera att inloggnings kontot har tillräcklig behörighet för att få åtkomst till SQL-databasen.


### <a name="error-code-sqlopenconnectiontimeout"></a>Felkod: SqlOpenConnectionTimeout

- **Meddelande**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Orsak**: problemet kan vara ett tillfälligt fel i SQL Database.

- **Rekommendation**: försök utföra åtgärden igen för att uppdatera anslutnings strängen för den länkade tjänsten med ett större timeout-värde för anslutningen.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Felkod: SqlAutoCreateTableTypeMapFailed

- **Meddelande**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Orsak**: tabellen för Autoskapa inte uppfyller käll kravet.

- **Rekommendation**: uppdatera kolumn typen i *mappningar* eller skapa mottagar tabellen manuellt på mål servern.


### <a name="error-code-sqldatatypenotsupported"></a>Felkod: SqlDataTypeNotSupported

- **Meddelande**: `A database operation failed. Check the SQL errors.`

- **Orsak**: om problemet uppstår i SQL-källan och felet är relaterat till SqlDateTime-spill, överskrider data värdet logik typ intervall (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM).

- **Rekommendation**: omvandla typen till STRÄNGEN i SQL-frågan för källan eller ändra kolumn typen till *sträng* i kolumn mappningen kopiera aktivitet.

- **Orsak**: om problemet uppstår i SQL-sinken och felet är relaterat till SqlDateTime overflow överskrider datavärdet det tillåtna intervallet i tabellen Sink.

- **Rekommendation**: uppdatera motsvarande kolumn typ till *datetime2* -typen i tabellen Sink.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Felkod: SqlInvalidDbStoredProcedure

- **Meddelande**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Orsak**: den angivna lagrade proceduren är ogiltig. Orsaken kan vara att den lagrade proceduren inte returnerar några data.

- **Rekommendation**: verifiera den lagrade proceduren med hjälp av SQL-verktyg. Se till att den lagrade proceduren kan returnera data.


### <a name="error-code-sqlinvaliddbquerystring"></a>Felkod: SqlInvalidDbQueryString

- **Meddelande**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Orsak**: den angivna SQL-frågan är ogiltig. Orsaken kan vara att frågan inte returnerar några data.

- **Rekommendation**: validera SQL-frågan med hjälp av SQL-verktyg. Se till att frågan kan returnera data.


### <a name="error-code-sqlinvalidcolumnname"></a>Felkod: SqlInvalidColumnName

- **Meddelande**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Orsak**: det går inte att hitta kolumnen eftersom konfigurationen kan vara felaktig.

- **Rekommendation**: verifiera kolumnen i frågan, *strukturen* i data uppsättningen och *mappningar* i aktiviteten.


### <a name="error-code-sqlbatchwritetimeout"></a>Felkod: SqlBatchWriteTimeout

- **Meddelande**: `Timeouts in SQL write operation.`

- **Orsak**: problemet kan bero på ett tillfälligt fel i SQL Database.

- **Rekommendation**: försök igen. Kontakta Azure SQL-supporten om problemet kvarstår.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Felkod: SqlBatchWriteTransactionFailed

- **Meddelande**: `SQL transaction commits failed.`

- **Orsak**: om undantags informationen hela tiden indikerar en transaktions-timeout, är nätverks fördröjningen mellan integrerings körningen och databasen större än standard tröskelvärdet på 30 sekunder.

- **Rekommendation**: uppdatera anslutnings STRÄNGEN för SQL-länkad tjänst med ett timeout-värde för *anslutning* som är lika med eller större än 120 och kör aktiviteten igen.

- **Orsak**: om undantags informationen i undantagsfall anger att SQL-anslutningen är bruten kan det vara ett tillfälligt nätverks fel eller ett problem med SQL Database-sidan.

- **Rekommendation**: försök utföra aktiviteten igen och granska måtten på SQL Database-sidan.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Felkod: SqlBulkCopyInvalidColumnLength

- **Meddelande**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Orsak**: SQL Mass kopiering misslyckades eftersom den fick en ogiltig kolumn längd från BCP-klienten (Mass kopierings program).

- **Rekommendation**: Aktivera fel tolerans funktionen på kopierings aktiviteten för att identifiera vilken rad som har påträffat problemet. Detta kan dirigera om problematiska rader till lagringen för ytterligare undersökning. Mer information finns i [fel tolerans för kopierings aktivitet i Azure Data Factory](./copy-activity-fault-tolerance.md).


### <a name="error-code-sqlconnectionisclosed"></a>Felkod: SqlConnectionIsClosed

- **Meddelande**: `The connection is closed by SQL Database.`

- **Orsak**: SQL-anslutningen stängs av SQL-databasen när en hög samtidig körning och servern avbryter anslutningen.

- **Rekommendation**: Försök ansluta igen. Kontakta Azure SQL-supporten om problemet kvarstår.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Fel meddelande: konverteringen misslyckades vid konvertering från en tecken sträng till uniqueidentifier

- **Symptom**: när du kopierar data från en tabell data källa (till exempel SQL Server) till Azure Synapse Analytics med hjälp av mellanlagrad kopiering och PolyBase får du följande fel meddelande:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Orsak**: Azure Synapse Analytics PolyBase kan inte konvertera en tom sträng till ett GUID.

- **Lösning**: ange alternativet **Använd typ som standard** till *falskt* under PolyBase-inställningar i kopians aktivitets mottagare.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Fel meddelande: förväntad datatyp: DECIMAL (x, x), felaktigt värde

- **Symptom**: när du kopierar data från en tabell data källa (till exempel SQL Server) till Azure Synapse Analytics med hjälp av mellanlagrad kopiering och PolyBase får du följande fel meddelande:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Orsak**: Azure Synapse Analytics PolyBase kan inte infoga en tom sträng (null-värde) i en decimal kolumn.

- **Lösning**: ange alternativet **Använd typ som standard** till falskt under PolyBase-inställningar i kopians aktivitets mottagare.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Fel meddelande: Java-undantags meddelande: HdfsBridge:: CreateRecordReader

- **Symptom**: du kopierar data till Azure Synapse Analytics genom att använda PolyBase och ta emot följande fel:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Orsak**: orsaken kan vara att schemat (total kolumn bredd) är för stort (större än 1 MB). Kontrol lera schemat för Azure Synapse Analytics-tabellen genom att lägga till storleken på alla kolumner:

    - Int = 4 byte
    - Bigint = 8 byte
    - Varchar (n), Char (n), binär (n), varbinary (n) = n byte
    - Nvarchar (n), nchar (n) = n * 2 byte
    - Datum = 6 byte
    - Datetime/(2), smalldatetime = 16 byte
    - DateTimeOffset = 20 byte
    - Decimal = 19 byte
    - Float = 8 byte
    - Money = 8 byte
    - Smallmoney = 4 byte
    - Real = 4 byte
    - Smallint = 2 byte
    - Tid = 12 byte
    - Tinyint = 1 byte

- **Lösning**: 
    - Minska kolumn bredden till mindre än 1 MB.
    - Eller Använd en Mass infognings metod genom att inaktivera PolyBase.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Fel meddelande: villkoret som anges med HTTP-villkorliga huvud (a) är inte uppfyllt

- **Symptom**: du använder SQL-fråga för att hämta data från Azure Synapse Analytics och ta emot följande fel:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Orsak**: Azure Synapse Analytics påträffade ett problem vid förfrågan till den externa tabellen i Azure Storage.

- **Lösning**: kör samma fråga i SQL Server Management Studio (SSMS) och kontrol lera om du får samma resultat. Om du gör det kan du öppna ett support ärende för Azure Synapse Analytics och tillhandahålla din Azure Synapse Analytics-Server och databas namn.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Prestanda nivån är låg och leads kan inte kopieras

- **Symptom**: du kopierar data till Azure SQL Database och får följande fel meddelande: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Orsak**: Azure SQL Database S1 har nått gränser för indata/utdata (I/O).

- **Lösning**: uppgradera Azure SQL Database prestanda nivå för att åtgärda problemet. 


### <a name="sql-table-cant-be-found"></a>Det går inte att hitta SQL-tabellen 

- **Symptom**: du kopierar data från hybrid till en lokal SQL Server tabell och får följande fel meddelande:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Orsak**: det aktuella SQL-kontot har inte tillräckliga behörigheter för att köra förfrågningar som utfärdats av .net SqlBulkCopy. WriteToServer.

- **Lösning**: växla till ett mer PRIVILEGIE rad SQL-konto.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Fel meddelande: sträng data eller binära data trunkeras

- **Symptom**: ett fel uppstår när du kopierar data till en lokal Azure SQL Server-tabell. 

- **Orsak**: schema definitionen för CX SQL-tabellen innehåller en eller flera kolumner med mindre längd än förväntat.

- **Lösning**: Lös problemet genom att prova följande:

    1. Om du vill felsöka vilka rader som har problemet använder du [fel toleransen](./copy-activity-fault-tolerance.md)för SQL-Sink, särskilt "redirectIncompatibleRowSettings."

        > [!NOTE]
        > Fel toleransen kan kräva ytterligare körnings tid, vilket kan leda till högre kostnader.

    2. Kontrol lera de omdirigerade data mot SQL Table schema kolumn längd för att se vilka kolumner som behöver uppdateras.

    3. Uppdatera tabell schemat enligt detta.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Felkod: AzureTableDuplicateColumnsFromSource

- **Meddelande**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Orsak**: duplicerade käll kolumner kan bero på någon av följande orsaker:
   * Du använder-databasen som källa och använda tabell kopplingar.
   * Du har ostrukturerade CSV-filer med dubbletter av kolumn namn i rubrik raden.

- **Rekommendation**: dubbel kontroll och korrigera käll kolumnerna om det behövs.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Felkod: DB2DriverRunFailed

- **Meddelande**: `Error thrown from driver. Sql code: '%code;'`

- **Orsak**: om fel meddelandet innehåller STRÄNGEN "SQLSTATE = 51002 SQLCODE =-805" följer du "Tip" i [Kopiera data från DB2 med hjälp av Azure Data Factory](./connector-db2.md#linked-service-properties).

- **Rekommendation**: försök att ange "NULLID" i `packageCollection`  egenskapen.


## <a name="delimited-text-format"></a>Avgränsat textformat

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Felkod: DelimitedTextColumnNameNotAllowNull

- **Meddelande**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Orsak**: när ' firstRowAsHeader ' anges i aktiviteten används den första raden som kolumn namn. Det här felet innebär att den första raden innehåller ett tomt värde (till exempel Kolumna, ColumnB).

- **Rekommendation**: kontrol lera den första raden och korrigera värdet om det är tomt.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Felkod: DelimitedTextMoreColumnsThanDefined

- **Meddelande**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Orsaker och rekommendationer**: olika orsaker kan leda till det här felet. Kontrol lera nedan för möjlig orsaks analys och relaterad rekommendation.

  | Orsaka analys                                               | Rekommendation                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Antalet felaktiga rader i kolumnen är större än den första radens kolumn antal. Det kan ha orsakats av ett data problem eller felaktiga inställningar för kolumn avgränsare eller citat tecken. | Hämta rad antalet från fel meddelandet, kontrol lera radens kolumn och korrigera data. |
  | Om det förväntade kolumn antalet är "1" i ett fel meddelande kan du ha angett fel komprimerings-eller format inställningar, vilket orsakade Data Factory att parsa filerna felaktigt. | Kontrol lera format inställningarna för att se till att de matchar källfilerna. |
  | Om din källa är en mapp kan filerna i den angivna mappen ha ett annat schema. | Kontrol lera att filerna i den angivna mappen har ett identiskt schema. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service och Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Felkod: DynamicsCreateServiceClientError

- **Meddelande**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Orsak**: problemet är ett tillfälligt problem på Dynamics-Server sidan.

- **Rekommendation**: kör pipelinen igen. Försök att minska parallellitet om det Miss lyckas igen. Kontakta Dynamics-supporten om problemet kvarstår.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Kolumner som saknas när du importerar ett schema eller för hands versions data

- **Symptom**: vissa kolumner saknas när du importerar ett schema eller för hands versions data. Fel meddelande: `The valid structure information (column name and type) are required for Dynamics source.`

- **Orsak**: det här problemet är avsiktligt eftersom data Factory inte kan visa kolumner som inte innehåller några värden i de första 10 posterna. Kontrol lera att de kolumner som du har lagt till har rätt format. 

- **Rekommendation**: Lägg till kolumnerna manuellt på fliken mappning.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Felkod: DynamicsMissingTargetForMultiTargetLookupField

- **Meddelande**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Orsak**: mål kolumnen finns inte i källan eller kolumn mappningen.

- **Rekommendation**:  
  1. Se till att källan innehåller mål kolumnen. 
  2. Lägg till mål kolumnen i kolumn mappningen. Se till att kolumnen Sink är i formatet *{FieldName} @EntityReference*.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Felkod: DynamicsInvalidTargetForMultiTargetLookupField

- **Meddelande**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Orsak**: fel enhets namn anges som målentiteten för ett söknings fält i flera mål.

- **Rekommendation**: Ange ett giltigt entitetsnamn för uppslags fältet för flera mål.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Felkod: DynamicsInvalidTypeForMultiTargetLookupField

- **Meddelande**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Orsak**: värdet i kolumnen Target är inte en sträng.

- **Rekommendation**: Ange en giltig sträng i kolumnen för söknings mål i flera mål.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Felkod: DynamicsFailedToRequetServer

- **Meddelande**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Orsak**: Dynamics-servern är instabil eller otillgänglig, eller så har nätverket problem.

- **Rekommendation**: kontrol lera nätverks anslutningen eller kontrol lera i Dynamics Server-loggen om du vill ha mer information. Kontakta Dynamics support om du vill ha mer hjälp.
  

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Felkod: FtpFailedToConnectToFtpServer

- **Meddelande**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Orsak**: en felaktig länkad tjänst typ kan användas för FTP-servern, till exempel att använda den länkade FTP-tjänsten (SFTP) för att ansluta till en FTP-server.

- **Rekommendation**: kontrol lera mål serverns port. FTP använder port 21.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Felkod: HttpFileFailedToRead

- **Meddelande**: `Failed to read data from http server. Check the error from http server：%message;`

- **Orsak**: det här felet uppstår när Azure Data Factory pratar med HTTP-servern, men det går inte att utföra http-begäran.

- **Rekommendation**: kontrol lera HTTP-statuskoden i fel meddelandet och åtgärda problemet med fjärrservern.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Felkod: ArgumentOutOfRangeException

- **Meddelande**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Orsak**: i Data Factory stöds datetime-värden i intervallet från 0001-01-01 00:00:00 till 9999-12-31 23:59:59. Oracle stöder dock ett större antal DateTime-värden, till exempel BC-Century eller min/SEK>59, vilket leder till att Data Factory Miss lyckas.

- **Rekommendation**: 

    Om du vill se om värdet i Oracle ligger inom intervallet för Data Factory kör du `select dump(<column name>)` . 

    Om du vill lära dig byte-sekvensen i resultatet, se [hur är datum lagrade i Oracle?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle).


## <a name="orc-format"></a>ORC-format

### <a name="error-code-orcjavainvocationexception"></a>Felkod: OrcJavaInvocationException

- **Meddelande**: `An error occurred when invoking Java, message: %javaException;.`
- **Orsaker och rekommendationer**: olika orsaker kan leda till det här felet. Kontrol lera nedan för möjlig orsaks analys och relaterad rekommendation.

    | Orsaka analys                                               | Rekommendation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | När fel meddelandet innehåller strängarna "Java. lang. OutOfMemory", "Java heap Space" och "doubleCapacity", är det vanligt vis ett minnes hanterings problem i en gammal version av integration Runtime. | Om du använder egen värd Integration Runtime rekommenderar vi att du uppgraderar till den senaste versionen. |
    | När fel meddelandet innehåller strängen "Java. lang. OutOfMemory" har integrerings körningen inte tillräckligt med resurser för att bearbeta filerna. | Begränsa samtidiga körningar i integration Runtime. För IR med egen värd skalar du upp till en kraftfull dator med minne som är lika med eller större än 8 GB. |
    |När fel meddelandet innehåller strängen "NullPointerReference" kan orsaken vara ett tillfälligt fel. | Försök att utföra åtgärden igen. Kontakta supporten om problemet kvarstår. |
    | När fel meddelandet innehåller strängen "BufferOverflowException" kan orsaken vara ett tillfälligt fel. | Försök att utföra åtgärden igen. Kontakta supporten om problemet kvarstår. |
    | När fel meddelandet innehåller strängen "Java. lang. ClassCastException:org. apache. Hadoop. Hive. serde2. io. HiveCharWritable kan inte omvandlas till org. apache. Hadoop. io. text", orsaken kan vara ett typ konverterings problem i Java Runtime. Det innebär vanligt vis att källdata inte kan hanteras korrekt i Java Runtime. | Detta är ett data problem. Försök att använda en sträng i stället för char eller varchar i ORC format data. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Felkod: OrcDateTimeExceedLimit

- **Meddelande**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Orsak**: om datetime-värdet är "0001-01-01 00:00:00" kan det bero på skillnaderna mellan [Juliansk-kalendern och den gregorianska kalendern](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Rekommendation**: kontrol lera Ticket-värdet och Undvik att använda datetime-värdet 0001-01-01 00:00:00.


## <a name="parquet-format"></a>Parquet-format

### <a name="error-code-parquetjavainvocationexception"></a>Felkod: ParquetJavaInvocationException

- **Meddelande**: `An error occurred when invoking java, message: %javaException;.`

- **Orsaker och rekommendationer**: olika orsaker kan leda till det här felet. Kontrol lera nedan för möjlig orsaks analys och relaterad rekommendation.

    | Orsaka analys                                               | Rekommendation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | När fel meddelandet innehåller strängarna "Java. lang. OutOfMemory", "Java heap Space" och "doubleCapacity", är det vanligt vis ett minnes hanterings problem i en gammal version av Integration Runtime. | Om du använder IR med egen värd och versionen är tidigare än 3.20.7159.1, rekommenderar vi att du uppgraderar till den senaste versionen. |
    | När fel meddelandet innehåller strängen "Java. lang. OutOfMemory" har integrerings körningen inte tillräckligt med resurser för att bearbeta filerna. | Begränsa samtidiga körningar i integration Runtime. För IR med egen värd skalar du upp till en kraftfull dator med minne som är lika med eller större än 8 GB. |
    | När fel meddelandet innehåller strängen "NullPointerReference" kan det vara ett tillfälligt fel. | Försök att utföra åtgärden igen. Kontakta supporten om problemet kvarstår. |

### <a name="error-code-parquetinvalidfile"></a>Felkod: ParquetInvalidFile

- **Meddelande**: `File is not a valid Parquet file.`

- **Orsak**: det här är ett problem med Parquet-filen.

- **Rekommendation**: kontrol lera om indata är en giltig Parquet-fil.


### <a name="error-code-parquetnotsupportedtype"></a>Felkod: ParquetNotSupportedType

- **Meddelande**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Orsak**: Parquet-formatet stöds inte i Azure Data Factory.

- **Rekommendation**: dubbelt kontrol lera källdata genom att gå till [fil format och komprimerings-codecar som stöds genom kopierings aktivitet i Azure Data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Felkod: ParquetMissedDecimalPrecisionScale

- **Meddelande**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Orsak**: tal precisionen och skalan parsades, men ingen sådan information angavs.

- **Rekommendation**: källan returnerar inte rätt precisions-och skalnings information. Kontrol lera informationen i ärende kolumnen.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Felkod: ParquetInvalidDecimalPrecisionScale

- **Meddelande**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Orsak**: schemat är ogiltigt.

- **Rekommendation**: mer precision och skalning finns i kolumnen ärende.


### <a name="error-code-parquetcolumnnotfound"></a>Felkod: ParquetColumnNotFound

- **Meddelande**: `Column %column; does not exist in Parquet file.`

- **Orsak**: käll schemat stämmer inte överens med Sink-schemat.

- **Rekommendation**: kontrol lera mappningarna i aktiviteten. Kontrol lera att käll kolumnen kan mappas till rätt Sink-kolumn.


### <a name="error-code-parquetinvaliddataformat"></a>Felkod: ParquetInvalidDataFormat

- **Meddelande**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Orsak**: data kan inte konverteras till den typ som anges i mappningar. källa.

- **Rekommendation**: dubbelklicka på källdata eller ange rätt datatyp för den här kolumnen i kolumn mappningen kopiera aktivitet. Mer information finns i [fil format och komprimerings-codecar som stöds genom kopierings aktivitet i Azure Data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Felkod: ParquetDataCountNotMatchColumnCount

- **Meddelande**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Orsak**: ett matchnings fel mellan käll kolumnens antal och antalet Sink-kolumner.

- **Rekommendation**: dubbel kontroll för att se till att käll kolumnernas antal är samma som kolumnen Sink i mappningen.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Felkod: ParquetDataTypeNotMatchColumnType

- **Meddelande**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Orsak**: data från källan kan inte konverteras till den typ som har definierats i mottagaren.

- **Rekommendation**: Ange en korrekt typ i mappning. Sink.


### <a name="error-code-parquetbridgeinvaliddata"></a>Felkod: ParquetBridgeInvalidData

- **Meddelande**: `%message;`

- **Orsak**: datavärdet har överskridit gränsen.

- **Rekommendation**: försök igen. Kontakta oss om problemet kvarstår.


### <a name="error-code-parquetunsupportedinterpretation"></a>Felkod: ParquetUnsupportedInterpretation

- **Meddelande**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Orsak**: det här scenariot stöds inte.

- **Rekommendation**: ' ParquetInterpretFor ' får inte vara ' sparkSql '.


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Felkod: ParquetUnsupportFileLevelCompressionOption

- **Meddelande**: `File level compression is not supported for Parquet.`

- **Orsak**: det här scenariot stöds inte.

- **Rekommendation**: ta bort CompressionType i nytto lasten.


### <a name="error-code-usererrorjniexception"></a>Felkod: UserErrorJniException

- **Meddelande**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Orsak**: det går inte att skapa en Java Virtual Machine (JVM) eftersom vissa ogiltiga (globala) argument har angetts.

- **Rekommendation**: Logga in på datorn som är värd för *varje nod* i din egen värd-IR. Kontrol lera att systemvariabeln är korrekt inställd, enligt följande: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Starta om alla IR-noder och kör sedan pipelinen igen.


### <a name="arithmetic-overflow"></a>Aritmetiskt spill

- **Symptom**: fel meddelande inträffade när du kopierar Parquet-filer: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Orsak**: för närvarande är bara decimalen av precision <= 38 och längden på heltals delen <= 20 stöd för när du kopierar filer från Oracle till Parquet. 

- **Lösning**: som en lösning kan du konvertera alla kolumner med det här problemet till varchar2.


### <a name="no-enum-constant"></a>Ingen Enum-konstant

- **Symptom**: fel meddelande inträffade när du kopierar data till Parquet-format: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , eller: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Orsak**: 

    Problemet kan bero på blank steg eller specialtecken som inte stöds (t. ex.,; {} () \n\t =) i kolumn namnet eftersom Parquet inte stöder sådant format. 

    Ett kolumn namn som *contoso (test)* kommer till exempel att parsa typen inom hakparenteser från [kod](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . Felet orsakas av att det inte finns någon sådan "test"-typ.

    Om du vill kontrol lera vilka typer som stöds går du till [webbplatsen GitHub Apache/Parquet-Mr](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Lösning**: 

    Dubbel kontroll för att se om:
    - Det finns blank steg i kolumn namnet för mottagaren.
    - Den första raden med blank steg används som kolumn namn.
    - Typen OriginalType stöds. Försök att undvika att använda följande specialtecken: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Felkod: RestSinkCallFailed

- **Meddelande**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Orsak**: det här felet uppstår när Azure Data Factory pratar med REST-slutpunkten över http-protokollet, och det går inte att utföra begäran.

- **Rekommendation**: kontrol lera HTTP-statuskoden eller meddelandet i fel meddelandet och åtgärda problemet med fjärrservern.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Oväntat nätverks svar från REST Connector

- **Symptom**: slut punkten tar ibland emot ett oväntat svar (400, 401, 403, 500) från rest-anslutningen.

- **Orsak**: rest source Connector använder URL: en och http-metoden/sidhuvudet/texten från den länkade tjänsten/data uppsättningen/kopierings källan som parametrar när en http-begäran konstrueras. Problemet orsakades troligen av några misstag i en eller flera angivna parametrar.

- **Lösning**: 
    - Använd "sväng" i ett kommando tolks fönster för att se om parametern är orsak (**Accept** -och **User-Agent** -huvuden ska alltid ingå):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Om kommandot returnerar samma oväntade svar, korrigerar du föregående parametrar med "sväng" tills det returnerar det förväntade svaret. 

      Du kan också använda "sväng--Help" för mer avancerad användning av kommandot.

    - Kontakta Microsoft-supporten om du vill ha ytterligare fel sökning om endast Data Factory REST-anslutaren returnerar ett oväntat svar.
    
    - Observera att "sväng" kanske inte lämpar sig för att återskapa ett verifierings problem för SSL-certifikat. I vissa fall kördes kommandot "klammer" utan att det påträffades några verifierings problem i SSL-certifikatet. Men när samma URL körs i en webbläsare returneras inget SSL-certifikat för klienten för att upprätta förtroende med servern.

      Verktyg som **Postman** och **Fiddler** rekommenderas för föregående fall.


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Felkod: SftpOperationFail

- **Meddelande**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Orsak**: ett problem med SFTP-åtgärden.

- **Rekommendation**: kontrol lera fel informationen från SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Felkod: SftpRenameOperationFail

- **Meddelande**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Orsak**: din SFTP-server har inte stöd för att byta namn på den temporära filen.

- **Rekommendation**: Ange "useTempFileRename" som falskt i kopians Sink för att inaktivera överföring till den temporära filen.


### <a name="error-code-sftpinvalidsftpcredential"></a>Felkod: SftpInvalidSftpCredential

- **Meddelande**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **Orsak**: privat nyckel innehåll hämtas från Azure Key Vault eller SDK, men är inte korrekt kodat.

- **Rekommendation**:  

    Om innehållet i den privata nyckeln kommer från ditt nyckel valv, kan den ursprungliga nyckel filen fungera om du laddar upp den direkt till den länkade SFTP-tjänsten.

    Mer information finns i [Kopiera data från och till SFTP-servern med hjälp av Azure Data Factory](./connector-sftp.md#using-ssh-public-key-authentication). Innehållet i den privata nyckeln är Base64-kodat innehåll för den privata SSH-nyckeln.

    Koda *hela* den ursprungliga privata nyckel filen med base64-kodning och lagra den kodade strängen i nyckel valvet. Den ursprungliga privata nyckel filen är den som kan arbeta med den länkade SFTP-tjänsten om du väljer **Ladda upp** från filen.

    Här följer några exempel som du kan använda för att generera strängen:

    - Använd C#-kod:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Använd python-kod:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Använd ett base64-konverterings verktyg från tredje part. Vi rekommenderar verktyget [koda till base64-format](https://www.base64encode.org/) .

- **Orsak**: fel format för nyckel innehåll har valts.

- **Rekommendation**:  

    PKCS # 8-format privat SSH-nyckel (börja med "-----starta KRYPTERAd privat nyckel-----") stöds för närvarande inte för att få åtkomst till SFTP-servern i Data Factory. 

    Om du vill konvertera nyckeln till traditionellt SSH-nyckelpar, som börjar med "-----starta RSA PRIVATE KEY-----", kör du följande kommandon:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Orsak**: ogiltiga autentiseringsuppgifter eller innehåll för privat nyckel.

- **Rekommendation**: om du vill se om nyckel filen eller lösen ordet är korrekt, dubbelklickar du på med verktyg som WinSCP.

### <a name="sftp-copy-activity-failed"></a>Det gick inte att kopiera SFTP

- **Symptom**: 
  * Felkod: UserErrorInvalidColumnMappingColumnNotFound 
  * Fel meddelande: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Orsak**: källan innehåller inte en kolumn med namnet "AccMngr".

- **Lösning**: du kan kontrol lera om kolumnen "AccMngr" finns genom att dubbelklicka på data uppsättnings konfigurationen genom att mappa kolumnen mål data uppsättning.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Felkod: SftpFailedToConnectToSftpServer

- **Meddelande**: `Failed to connect to SFTP server '%server;'.`

- **Orsak**: om fel meddelandet innehåller strängen "Läs åtgärd för socket nådde tids gränsen efter 30 000 millisekunder", kan en möjlig orsak vara att en felaktig länkad tjänst typ används för SFTP-servern. Du kanske t. ex. använder den länkade FTP-tjänsten för att ansluta till SFTP-servern.

- **Rekommendation**: kontrol lera mål serverns port. Som standard använder SFTP port 22.

- **Orsak**: om fel meddelandet innehåller strängen "Server svaret innehåller inte SSH-protokoll-ID", en möjlig orsak är att SFTP-servern har begränsat anslutningen. Data Factory skapar flera anslutningar för att ladda ned från SFTP-servern parallellt, och ibland kommer den att drabbas av SFTP-server-begränsning. Vanligt vis returnerar olika servrar olika fel när de drabbas av begränsning.

- **Rekommendation**:  

    Ange det maximala antalet samtidiga anslutningar för SFTP-datauppsättningen som 1 och kör kopierings aktiviteten igen. Om aktiviteten lyckas kan du vara säker på att begränsningen är orsaken.

    Om du vill befordra det låga data flödet kontaktar du din SFTP-administratör för att öka antalet samtidiga anslutningar, eller så kan du göra något av följande:

    * Om du använder IR med egen värd lägger du till IP-adressen för den egna IR-datorn i listan över tillåtna.
    * Om du använder Azure IR lägger du till [Azure integration runtime IP-adresser](./azure-integration-runtime-ip-addresses.md). Om du inte vill lägga till ett intervall med IP-adresser i listan över tillåtna IP-adresser använder du IR i stället.

## <a name="sharepoint-online-list"></a>SharePoint Online-lista

### <a name="error-code-sharepointonlineauthfailed"></a>Felkod: SharePointOnlineAuthFailed

- **Meddelande**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Orsak**: tjänstens huvud namn-ID och nyckel är kanske inte korrekt inställda.

- **Rekommendation**: kontrol lera ditt registrerade program (tjänstens huvud NAMNS-ID) och nyckel för att se om de är korrekt inställda.


## <a name="xml-format"></a>XML-format

### <a name="error-code-xmlsinknotsupported"></a>Felkod: XmlSinkNotSupported

- **Meddelande**: `Write data in XML format is not supported yet, choose a different format!`

- **Orsak**: en XML-datauppsättning användes som en mottagar data uppsättning i din kopierings aktivitet.

- **Rekommendation**: Använd en data uppsättning i ett annat format än den data uppsättningens data uppsättning.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Felkod: XmlAttributeColumnNameConflict

- **Meddelande**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Orsak**: ett attributvärde användes, vilket orsakade konflikten.

- **Rekommendation**: Ange ett annat värde för egenskapen "attributePrefix".


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Felkod: XmlValueColumnNameConflict

- **Meddelande**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Orsak**: ett av de underordnade elementen användes som kolumn namn för elementets värde.

- **Rekommendation**: Ange ett annat värde för egenskapen "valueColumn".


### <a name="error-code-xmlinvalid"></a>Felkod: XmlInvalid

- **Meddelande**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Orsak**: indata-XML-filen är inte korrekt formaterad.

- **Rekommendation**: korrigera XML-filen så att den blir korrekt formaterad.


## <a name="general-copy-activity-error"></a>Fel vid allmän kopierings aktivitet

### <a name="error-code-jrenotfound"></a>Felkod: JreNotFound

- **Meddelande**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Orsak**: den egna VÄRDbaserade IR-filen kan inte hitta Java Runtime. Java Runtime krävs för att läsa vissa källor.

- **Rekommendation**: kontrol lera din integration runtime-miljö, se [Använd integration runtime med egen värd](./format-parquet.md#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Felkod: WildcardPathSinkNotSupported

- **Meddelande**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Orsak**: Sink-datamängden stöder inte jokertecken.

- **Rekommendation**: kontrol lera data uppsättningen för mottagaren och skriv om sökvägen utan att använda ett jokertecken.


### <a name="fips-issue"></a>FIPS-problem

- **Symptom**: kopierings aktiviteten Miss lyckas på en FIPS-aktiverad IR-dator med följande fel meddelande: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Orsak**: det här felet kan inträffa när du kopierar data med kopplingar, till exempel Azure Blob, SFTP och så vidare. FIPS (Federal Information Processing Standards) definierar en viss uppsättning kryptografiska algoritmer som får användas. När FIPS-läge är aktiverat på datorn blockeras vissa kryptografiska klasser som kopierar aktivitet i vissa scenarier.

- **Lösning**: Lär dig [varför vi inte rekommenderar "FIPS-läge" längre](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)och utvärdera om du kan inaktivera FIPS på din egen IR-dator.

    Om du bara vill låta Azure Data Factory kringgå FIPS och göra så att aktiviteten körs lyckas gör du följande:

    1. Öppna mappen där IR för egen värd är installerad. Sökvägen är vanligt vis c:\Program\Microsoft *integration runtime \<IR version> \Shared*.

    2. Öppna filen *diawp.exe.config* och Lägg sedan till, i slutet av `<runtime>` avsnittet, som du `<enforceFIPSPolicy enabled="false"/>` ser här:

        ![Skärm bild av en del av diawp.exe.config-filen som visar FIPS inaktiverat.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Spara filen och starta sedan om IR-datorn med egen värd.


## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&en sida](/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)