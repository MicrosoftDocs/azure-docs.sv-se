---
title: Felsöka Azure Data Factory anslutningsappar
description: Lär dig hur du felsöker anslutningsproblem i Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/13/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 21b5522f07519e9a0c3353cb2463e0ec49063f34
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713434"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Felsöka Azure Data Factory anslutningsappar

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs vanliga sätt att felsöka problem Azure Data Factory anslutningsappar.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Felkod: AzureBlobOperationFailed

- **Meddelande:**"Blobåtgärden misslyckades. ContainerName: %containerName;, path: %path;."

- **Orsak:** Ett problem med Blob Storage åtgärden.

- **Rekommendation:** Information om fel finns i [Blob Storage felkoder.](/rest/api/storageservices/blob-service-error-codes) Kontakta Blob Storage om du behöver mer hjälp.


### <a name="invalid-property-during-copy-activity"></a>Ogiltig egenskap under kopieringsaktivitet

- **Meddelande:**`Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Orsak:** Den typ som definierats i datauppsättningen är inkonsekvent med den käll- eller mottagaretyp som definieras i kopieringsaktiviteten.

- **Lösning:** Redigera JSON-definitionen för datauppsättningen eller pipelinen för att göra typerna konsekventa och kör sedan distributionen igen.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Felmeddelande: Begärandestorleken är för stor

- **Symptom:** När du kopierar data Azure Cosmos DB med en standardstorlek för skrivbatch får du följande fel: `Request size is too large.`

- **Orsak:** Azure Cosmos DB begränsar storleken på en enskild begäran till 2 MB. Formeln är request *size = single document size write batch \* size*. Om dokumentstorleken är stor resulterar standardbeteendet i en begärandestorlek som är för stor. Du kan justera skrivningsbatchstorleken.

- **Lösning:** Minska värdet för skrivningsbatchstorlek i *kopieringsaktivitetens* mottagare (standardvärdet är 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Felmeddelande: Överträdelse av unik indexbegränsning

- **Symptom:** När du kopierar data Azure Cosmos DB visas följande fel:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Orsak:** Det finns två möjliga orsaker:

    - Orsak 1: Om du använder **Infoga** som skrivbeteende innebär det här felet att dina källdata har rader eller objekt med samma ID.
    - Orsak 2: Om du använder **Upsert** som skrivbeteende och anger en annan unik nyckel till containern innebär det här felet att dina källdata har rader eller objekt med olika ID:er men samma värde för den definierade unika nyckeln.

- **Lösning**: 

    - För orsak 1 ställer du **in Upsert** som skrivbeteende.
    - För orsak 2 kontrollerar du att varje dokument har ett annat värde för den definierade unika nyckeln.

### <a name="error-message-request-rate-is-large"></a>Felmeddelande: Förfrågningsfrekvensen är hög

- **Symptom:** När du kopierar data Azure Cosmos DB visas följande fel:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Orsak:** Antalet enheter för begärande (RU:er) som används är större än de tillgängliga RU:er som konfigurerats i Azure Cosmos DB. Information om hur Azure Cosmos DB beräknar RU:er finns i [Enheter för begäran i Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Lösning:** Prova någon av följande två lösningar:

    - Öka *antalet RU:er för* containern till ett större värde i Azure Cosmos DB. Den här lösningen förbättrar kopieringsaktivitetens prestanda, men den medför mer kostnader i Azure Cosmos DB. 
    - Minska *writeBatchSize* till ett mindre värde, till exempel 1000, och minska *parallelCopies* till ett mindre värde, till exempel 1. Den här lösningen minskar prestandan för kopieringskörningar, men den medför inte mer kostnader i Azure Cosmos DB.

### <a name="columns-missing-in-column-mapping"></a>Kolumner som saknas i kolumnmappning

- **Symptom:** När du importerar ett schema Azure Cosmos DB för kolumnmappning saknas vissa kolumner. 

- **Orsak:** Data Factory härrar schemat från de första 10 Azure Cosmos DB dokumenten. Om vissa dokumentkolumner eller egenskaper inte innehåller värden identifieras inte schemat av Data Factory och visas därför inte.

- **Lösning:** Du kan finjustera frågan enligt följande kod för att tvinga kolumnvärdena att visas i resultatuppsättningen med tomma värden. Anta att den *omöjliga* kolumnen saknas i de första 10 dokumenten). Du kan också lägga till kolumnen för mappning manuellt.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Felmeddelande: GuidRepresentation för läsaren är CSharpLegacy

- **Symptom:** När du kopierar data från Azure Cosmos DB MongoAPI eller MongoDB med fältet universell unik identifierare (UUID) får du följande fel:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Orsak:** Det finns två sätt att representera UUID i Binär JSON (BSON): UuidStardard och UuidLegacy. Som standard används UuidLegacy för att läsa data. Du får ett felmeddelande om dina UUID-data i MongoDB är UuidStandard.

- **Lösning:** I MongoDB-anslutningssträngen lägger du till *alternativet uuidRepresentation=standard.* Mer information finns i [MongoDB-anslutningssträngen](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Felkod: CosmosDbSqlApiOperationFailed

- **Meddelande:**`CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Orsak:** Ett problem med CosmosDbSqlApi-åtgärden.

- **Rekommendation:** Information om fel finns i Azure Cosmos DB [hjälpdokument](../cosmos-db/troubleshoot-dot-net-sdk.md). Kontakta Azure Cosmos DB om du behöver mer hjälp.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Felmeddelande: Den underliggande anslutningen stängdes: Det gick inte att upprätta förtroenderelationen för den säkra SSL/TLS-kanalen.

- **Symptom:** aktiviteten Kopiera misslyckas med följande fel: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Orsak:** Certifikatverifieringen misslyckades under TLS-handskakningen.

- **Lösning:** Som en lösning kan du använda den mellanstaderade kopian för att hoppa Transport Layer Security TLS-verifieringen för Azure Data Lake Storage Gen1. Du måste återskapa det här problemet och samla in nätverksövervakarspårningen (netmon) och sedan engagera ditt nätverksteam för att kontrollera den lokala nätverkskonfigurationen.

    ![Diagram över Azure Data Lake Storage Gen1 för felsökning av problem.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Felmeddelande: Fjärrservern returnerade ett fel: (403) Förbjuden

- **Symptom:** aktiviteten Kopiera misslyckas med följande fel: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Orsak:** En möjlig orsak är att tjänstens huvudnamn eller hanterade identitet som du använder inte har behörighet att komma åt vissa mappar eller filer.

- **Lösning:** Bevilja lämpliga behörigheter till alla mappar och undermappar som du behöver kopiera. Mer information finns i Kopiera [data till eller från Azure Data Lake Storage Gen1 med Azure Data Factory](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Felmeddelande: Det gick inte att hämta åtkomsttoken med hjälp av tjänstens huvudnamn. ADAL-fel: service_unavailable

- **Symptom:** aktiviteten Kopiera misslyckas med följande fel:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Orsak:** När tjänsttokenservern (STS) som ägs av Azure Active Directory inte är tillgänglig, innebär det att den är för upptagen för att hantera begäranden och returnerar HTTP-fel 503. 

- **Lösning:** Kör kopieringsaktiviteten igen efter flera minuter.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Felkod: ADLSGen2OperationFailed

- **Meddelande:**`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Orsaker och rekommendationer:** Olika orsaker kan leda till det här felet. Kontrollera listan nedan för möjliga orsaksanalyser och relaterade rekommendationer.

  | Orsaksanalys                                               | Rekommendation                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Om Azure Data Lake Storage Gen2 ett fel som anger att en åtgärd misslyckades.| Kontrollera det detaljerade felmeddelande som Azure Data Lake Storage Gen2. Om felet är ett tillfälligt fel försöker du igen. Om du behöver mer hjälp kontaktar Azure Storage supporten och anger id:t för begäran i felmeddelandet. |
  | Om felmeddelandet innehåller strängen "Förbjuden" kanske tjänstens huvudnamn eller hanterade identitet som du använder inte har tillräcklig behörighet för att komma åt Azure Data Lake Storage Gen2. | Information om hur du felsöker det [här felet finns i Kopiera och transformera data Azure Data Lake Storage Gen2 med hjälp av Azure Data Factory](./connector-azure-data-lake-storage.md#service-principal-authentication). |
  | Om felmeddelandet innehåller strängen "InternalServerError" returneras felet av Azure Data Lake Storage Gen2. | Felet kan bero på ett tillfälligt fel. Om den är det kan du försöka att utföra åtgärden igen. Om problemet kvarstår kontaktar du Azure Storage supporten och anger id:t för begäran från felmeddelandet. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Begäran om att Azure Data Lake Storage Gen2 konto orsakade ett timeout-fel

- **Meddelande:** 
  * Felkod = `UserErrorFailedBlobFSOperation`
  * Felmeddelande = `BlobFS operation failed for: A task was canceled.`

- **Orsak:** Problemet orsakas av Azure Data Lake Storage Gen2 tidsgränsfel för mottagare, vilket vanligtvis inträffar på den lokala Integration Runtime-datorn.

- **Rekommendation**: 

    - Placera din IR-dator med egen värd och Azure Data Lake Storage Gen2 i samma region, om möjligt. Detta kan bidra till att undvika ett slumpmässigt timeout-fel och ge bättre prestanda.

    - Kontrollera om det finns en särskild nätverksinställning, till exempel ExpressRoute, och kontrollera att nätverket har tillräckligt med bandbredd. Vi rekommenderar att du sänker inställningen För samtidiga IR-jobb med egen värd när den totala bandbredden är låg. Detta kan bidra till att undvika konkurrens om nätverksresurser i flera samtidiga jobb.

    - Om filstorleken är måttlig eller liten använder du en mindre blockstorlek för icke-binär kopia för att minimera ett sådant timeout-fel. Mer information finns i [Blob Storage Put Block](/rest/api/storageservices/put-block).

       Om du vill ange den anpassade blockstorleken redigerar du egenskapen i JSON-filredigeraren enligt följande:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Azure Files storage

### <a name="error-code-azurefileoperationfailed"></a>Felkod: AzureFileOperationFailed

- **Meddelande:**`Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Orsak:** Ett problem med Azure Files lagringsåtgärden.

- **Rekommendation:** Information om fel finns i [Azure Files hjälp](/rest/api/storageservices/file-service-error-codes). Kontakta Azure Files om du behöver mer hjälp.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, Azure SQL Database och SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Felkod: SqlFailedToConnect

- **Meddelande:**`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Orsaker och rekommendationer:** Olika orsaker kan leda till det här felet. Kontrollera listan nedan för möjliga orsaksanalyser och relaterade rekommendationer.

    | Orsaksanalys                                               | Rekommendation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Om Azure SQL exempel innehåller strängen "SqlErrorNumber=47073" betyder det att offentlig nätverksåtkomst nekas i anslutningsinställningen. | I den Azure SQL brandväggen anger du **alternativet Neka offentlig nätverksåtkomst** till *Nej.* Mer information finns i [Azure SQL anslutningsinställningar.](../azure-sql/database/connectivity-settings.md#deny-public-network-access) |
    | Om Azure SQL innehåller en SQL-felkod, till exempel "SqlErrorNumber=[errorcode]", kan du gå Azure SQL felsökningsguiden. | En rekommendation finns i [Felsöka anslutningsproblem och andra fel med Azure SQL Database och Azure SQL Managed Instance](../azure-sql/database/troubleshoot-common-errors-issues.md). |
    | Kontrollera om port 1433 finns i listan över tillåtna brandväggar. | Mer information finns i Portar [som används av SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Om felmeddelandet innehåller strängen "SqlException" SQL Database felet att en viss åtgärd misslyckades. | Om du vill ha mer information söker du efter SQL-felkod [i Databasmotorfel.](/sql/relational-databases/errors-events/database-engine-events-and-errors) Kontakta supporten om du Azure SQL hjälp. |
    | Om det här är ett tillfälligt problem (till exempel en instable-nätverksanslutning) lägger du till ett nytt försök i aktivitetsprincipen för att åtgärda problemet. | Mer information finns i [Pipelines och aktiviteter i Azure Data Factory](./concepts-pipelines-activities.md#activity-policy). |
    | Om felmeddelandet innehåller strängen "Client with IP address "... " (Klient med IP-adress ... ) är inte tillåtet att komma åt servern" och du försöker ansluta till Azure SQL Database orsakas felet vanligtvis av ett fel Azure SQL Database brandväggen. | I brandväggskonfigurationen Azure SQL Server aktiverar du alternativet **Tillåt Azure-tjänster och resurser att komma åt den här** servern. Mer information finns i Azure SQL Database [och Azure Synapse IP-brandväggsregler.](../azure-sql/database/firewall-configure.md) |
    
### <a name="error-code-sqloperationfailed"></a>Felkod: SqlOperationFailed

- **Meddelande:**`A database operation failed. Please search error to get more details.`

- **Orsaker och rekommendationer:** Olika orsaker kan leda till det här felet. Kontrollera listan nedan för möjliga orsaksanalyser och relaterade rekommendationer.

    | Orsaksanalys                                               | Rekommendation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Om felmeddelandet innehåller strängen "SqlException" SQL Database ett fel som anger att en viss åtgärd misslyckades. | Om SQL-felet inte är tydligt kan du försöka ändra databasen till den senaste kompatibilitetsnivån "150". Det kan leda till SQL-fel i den senaste versionen. Mer information finns i [dokumentationen](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> Om du vill ha mer information om felsökning av SQL-problem kan du söka efter SQL-felkod [i Databasmotorfel.](/sql/relational-databases/errors-events/database-engine-events-and-errors) Kontakta supporten om du Azure SQL hjälp. |
    | Om felmeddelandet innehåller strängen "PdwManagedToNativeInteropException" orsakas det vanligtvis av ett matchningsfel mellan kolumnstorlekarna för källan och mottagaren. | Kontrollera storleken på både käll- och mottagare-kolumnerna. Kontakta supporten om du Azure SQL hjälp. |
    | Om felmeddelandet innehåller strängen "InvalidOperationException" orsakas det vanligtvis av ogiltiga indata. | Om du vill identifiera vilken rad som har påträffat problemet aktiverar du feltoleransfunktionen för kopieringsaktiviteten, som kan omdirigera problematiska rader till lagringen för vidare undersökning. Mer information finns i [Feltolerans för kopieringsaktivitet i Azure Data Factory](./copy-activity-fault-tolerance.md). |


### <a name="error-code-sqlunauthorizedaccess"></a>Felkod: SqlUnauthorizedAccess

- **Meddelande:**`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Orsak:** Autentiseringsuppgifterna är felaktiga eller så kan inloggningskontot inte komma åt SQL-databasen.

- **Rekommendation:** Kontrollera att inloggningskontot har tillräcklig behörighet för att komma åt SQL-databasen.


### <a name="error-code-sqlopenconnectiontimeout"></a>Felkod: SqlOpenConnectionTimeout

- **Meddelande:**`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Orsak:** Problemet kan vara ett tillfälligt fel i SQL Database.

- **Rekommendation:** Försök igen för att uppdatera den länkade tjänstanslutningssträngen med ett större tidsgränsvärde för anslutningen.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Felkod: SqlAutoCreateTableTypeMapFailed

- **Meddelande:**`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Orsak:** Autoskapingstabellen kan inte uppfylla källkravet.

- **Rekommendation:** Uppdatera kolumntypen i *mappningar*, eller skapa manuellt tabellen mottagare på målservern.


### <a name="error-code-sqldatatypenotsupported"></a>Felkod: SqlDataTypeNotSupported

- **Meddelande:**`A database operation failed. Check the SQL errors.`

- **Orsak:** Om problemet uppstår i SQL-källan och felet är relaterat till SqlDateTime-spill, överskrider datavärdet logiktypens intervall (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM).

- **Rekommendation:** Byt typ till strängen i SQL-källfrågan eller ändra kolumntypen till Sträng i kolumnmappningen för *kopieringsaktiviteten.*

- **Orsak:** Om problemet uppstår på SQL-mottagaren och felet är relaterat till SqlDateTime-spill, överskrider datavärdet det tillåtna intervallet i tabellen för mottagare.

- **Rekommendation:** Uppdatera motsvarande kolumntyp till *datetime2-typen* i tabellen för mottagare.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Felkod: SqlInvalidDbStoredProcedure

- **Meddelande:**`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Orsak:** Den angivna lagrade proceduren är ogiltig. Orsaken kan vara att den lagrade proceduren inte returnerar några data.

- **Rekommendation:** Verifiera den lagrade proceduren med hjälp av SQL-verktyg. Kontrollera att den lagrade proceduren kan returnera data.


### <a name="error-code-sqlinvaliddbquerystring"></a>Felkod: SqlInvalidDbQueryString

- **Meddelande:**`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Orsak:** Den angivna SQL-frågan är ogiltig. Orsaken kan vara att frågan inte returnerar några data.

- **Rekommendation:** Verifiera SQL-frågan med hjälp av SQL-verktyg. Kontrollera att frågan kan returnera data.


### <a name="error-code-sqlinvalidcolumnname"></a>Felkod: SqlInvalidColumnName

- **Meddelande:**`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Orsak:** Det går inte att hitta kolumnen eftersom konfigurationen kan vara felaktig.

- **Rekommendation:** Verifiera kolumnen i frågan, *strukturen i* datauppsättningen och *mappningarna* i aktiviteten.


### <a name="error-code-sqlbatchwritetimeout"></a>Felkod: SqlBatchWriteTimeout

- **Meddelande:**`Timeouts in SQL write operation.`

- **Orsak:** Problemet kan orsakas av ett tillfälligt fel i SQL-databasen.

- **Rekommendation:** Försök igen. Kontakta supporten om problemet kvarstår Azure SQL supporten.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Felkod: SqlBatchWriteTransactionFailed

- **Meddelande:**`SQL transaction commits failed.`

- **Orsak:** Om undantagsinformationen ständigt indikerar en transaktionstidsgräns är nätverksfördröjningen mellan Integration Runtime och databasen större än standardtröskelvärdet på 30 sekunder.

- **Rekommendation:** Uppdatera anslutningssträngen för  den SQL-länkade tjänsten med ett tidsgränsvärde för anslutningen som är lika med eller större än 120 och kör aktiviteten igen.

- **Orsak:** Om undantagsinformationen tillfälligt indikerar att SQL-anslutningen har brutits kan det bero på ett tillfälligt nätverksfel eller ett problem på SQL-databassidan.

- **Rekommendation:** Försök med aktiviteten igen och granska SQL-databassidans mått.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Felkod: SqlBulkCopyInvalidColumnLength

- **Meddelande:**`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Orsak:** SQL-masskopiering misslyckades eftersom den tog emot en ogiltig kolumnlängd från bcp-klienten (bulk copy program utility).

- **Rekommendation:** Du kan identifiera vilken rad som har påträffat problemet genom att aktivera feltoleransfunktionen för kopieringsaktiviteten. Detta kan omdirigera problematiska rader till lagringen för vidare undersökning. Mer information finns i [Feltolerans för kopieringsaktivitet i Azure Data Factory](./copy-activity-fault-tolerance.md).


### <a name="error-code-sqlconnectionisclosed"></a>Felkod: SqlConnectionIsClosed

- **Meddelande:**`The connection is closed by SQL Database.`

- **Orsak:** SQL-anslutningen stängs av SQL-databasen när en hög samtidig körning och servern avslutar anslutningen.

- **Rekommendation:** Försök att ansluta igen. Kontakta supporten om problemet kvarstår Azure SQL supporten.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Felmeddelande: Konverteringen misslyckades vid konvertering från en teckensträng till uniqueidentifier

- **Symptom:** När du kopierar data från en tabelldatakälla (till exempel SQL Server) till Azure Synapse Analytics med hjälp av mellanstegskopiering och PolyBase får du följande fel:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Orsak:** Azure Synapse Analytics PolyBase kan inte konvertera en tom sträng till ett GUID.

- **Lösning:** I kopieringsaktivitetens mottagare, under PolyBase-inställningar, anger du **standardalternativet för användningstyp** till *falskt.*


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Felmeddelande: Förväntad datatyp: DECIMAL(x,x), Störande värde

- **Symptom:** När du kopierar data från en tabelldatakälla (till exempel SQL Server) till Azure Synapse Analytics med hjälp av mellannivåkopiering och PolyBase får du följande fel:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Orsak:** Azure Synapse Analytics PolyBase kan inte infoga en tom sträng (null-värde) i en decimalkolumn.

- **Lösning:** I kopieringsaktivitetens mottagare, under PolyBase-inställningar, anger du **standardalternativet för användningstyp** till falskt.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Felmeddelande: Java-undantagsmeddelande: HdfsBridge::CreateRecordReader

- **Symptom:** Du kopierar data till Azure Synapse Analytics med hjälp av PolyBase och får följande fel:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Orsak:** Orsaken kan vara att schemat (total kolumnbredd) är för stort (större än 1 MB). Kontrollera schemat för måltabellen Azure Synapse Analytics genom att lägga till storleken på alla kolumner:

    - Int = 4 byte
    - Bigint = 8 byte
    - Varchar(n), char(n), binary(n), varbinary(n) = n bytes
    - Nvarchar(n), nchar(n) = n*2 byte
    - Datum = 6 byte
    - Datetime/(2), smalldatetime = 16 byte
    - Datetimeoffset = 20 byte
    - Decimal = 19 byte
    - Flyttal = 8 byte
    - Pengar = 8 byte
    - Small script = 4 byte
    - Verklig = 4 byte
    - Smallint = 2 byte
    - Tid = 12 byte
    - Tinyint = 1 byte

- **Lösning**: 
    - Minska kolumnbredden till mindre än 1 MB.
    - Eller använd en metod för massinfogning genom att inaktivera PolyBase.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Felmeddelande: Villkoret som anges med villkorsstyrda HTTP-huvuden uppfylls inte

- **Symptom:** Du använder SQL-fråga för att hämta data Azure Synapse Analytics och får följande fel:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Orsak:** Azure Synapse Analytics påträffade ett problem vid frågor till den externa tabellen i Azure Storage.

- **Lösning:** Kör samma fråga i SQL Server Management Studio (SSMS) och kontrollera om du får samma resultat. Om du har det öppnar du ett support ärende för Azure Synapse Analytics och anger Azure Synapse Analytics server och databasnamn.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Prestandanivån är låg och leder till kopieringsfel

- **Symptom:** Du kopierar data till Azure SQL Database och får följande fel: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Orsak:** Azure SQL Database s1 har drabbats av I/O-gränser (indata/utdata).

- **Lösning:** Uppgradera Azure SQL Database för att åtgärda problemet. 


### <a name="sql-table-cant-be-found"></a>Det går inte att hitta SQL-tabellen 

- **Symptom:** Du kopierar data från hybrid till en lokal SQL Server tabell och får följande fel:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Orsak:** Det aktuella SQL-kontot har inte tillräcklig behörighet för att köra begäranden som utfärdats av .NET SqlBulkCopy.WriteToServer.

- **Lösning:** Växla till ett mer privilegierat SQL-konto.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Felmeddelande: Strängdata eller binära data trunkeras

- **Symptom:** Ett fel uppstår när du kopierar data till en lokal Azure SQL Server-tabell. 

- **Orsak:** Schemadefinitionen för Cx SQL-tabellen har en eller flera kolumner med mindre längd än förväntat.

- **Lösning:** Prova följande för att lösa problemet:

    1. Om du vill felsöka vilka rader som har problemet använder du [feltolerans](./copy-activity-fault-tolerance.md)för SQL-mottagare, särskilt "redirectIncompatibleRowSettings".

        > [!NOTE]
        > Feltolerans kan kräva ytterligare körningstid, vilket kan leda till högre kostnader.

    2. Dubbelkolla omdirigerade data mot kolumnlängden för SQL-tabellschemat för att se vilka kolumner som behöver uppdateras.

    3. Uppdatera tabellschemat därefter.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Felkod: AzureTableDuplicateColumnsFromSource

- **Meddelande:**`Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Orsak:** Duplicerade källkolumner kan inträffa av någon av följande orsaker:
   * Du använder databasen som källa och tillämpade tabellkopplingar.
   * Du har ostrukturerade CSV-filer med duplicerade kolumnnamn på rubrikraden.

- **Rekommendation:** Dubbelkolla och åtgärda källkolumnerna efter behov.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Felkod: DB2DriverRunFailed

- **Meddelande:**`Error thrown from driver. Sql code: '%code;'`

- **Orsak:** Om felmeddelandet innehåller strängen "SQLSTATE=51002 SQLCODE=-805" följer du "Tips" i Kopiera data från [DB2](./connector-db2.md#linked-service-properties)med hjälp av Azure Data Factory .

- **Rekommendation:** Försök att ange "NULLID" i `packageCollection`  egenskapen .


## <a name="delimited-text-format"></a>Avgränsat textformat

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Felkod: DelimitedTextColumnNameNotAllowNull

- **Meddelande:**`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Orsak:** När "firstRowAsHeader" anges i aktiviteten används den första raden som kolumnnamn. Det här felet innebär att den första raden innehåller ett tomt värde (till exempel "ColumnA, ColumnB").

- **Rekommendation:** Kontrollera den första raden och åtgärda värdet om det är tomt.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Felkod: DelimitedTextMoreColumnsThanDefined

- **Meddelande:**`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Orsaker och rekommendationer:** Olika orsaker kan leda till det här felet. Kontrollera listan nedan för möjliga orsaksanalyser och relaterade rekommendationer.

  | Orsaksanalys                                               | Rekommendation                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Antalet problematiska rader i kolumnen är större än antalet kolumner på den första raden. Det kan bero på ett dataproblem eller felaktiga inställningar för kolumnavgränsare eller citattecken. | Hämta radantalet från felmeddelandet, kontrollera radens kolumn och åtgärda data. |
  | Om det förväntade kolumnantalet är "1" i ett felmeddelande kan du ha angett fel komprimerings- eller formatinställningar, vilket gjorde att Data Factory parsade dina filer felaktigt. | Kontrollera formatinställningarna för att se till att de matchar dina källfiler. |
  | Om källan är en mapp kan filerna under den angivna mappen ha ett annat schema. | Kontrollera att filerna i den angivna mappen har ett identiskt schema. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service och Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Felkod: DynamicsCreateServiceClientError

- **Meddelande:**`This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Orsak:** Problemet är ett tillfälligt problem på Dynamics-serversidan.

- **Rekommendation:** Kör pipelinen igen. Om det misslyckas igen kan du försöka minska parallellitet. Kontakta Dynamics-supporten om problemet kvarstår.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Saknade kolumner när du importerar ett schema eller förhandsgranskningsdata

- **Symptom:** Vissa kolumner saknas när du importerar ett schema eller förhandsgranskar data. Felmeddelande: `The valid structure information (column name and type) are required for Dynamics source.`

- **Orsak:** Det här problemet beror på att Data Factory inte kan visa kolumner som inte innehåller några värden i de första 10 posterna. Kontrollera att kolumnerna som du har lagt till har rätt format. 

- **Rekommendation:** Lägg till kolumnerna manuellt på mappningsfliken.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Felkod: DynamicsMissingTargetForMultiTargetLookupField

- **Meddelande:**`Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Orsak:** Målkolumnen finns inte i källan eller i kolumnmappningen.

- **Rekommendation**:  
  1. Kontrollera att källan innehåller målkolumnen. 
  2. Lägg till målkolumnen i kolumnmappningen. Kontrollera att kolumnen mottagare har formatet *{fieldName} @EntityReference*.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Felkod: DynamicsInvalidTargetForMultiTargetLookupField

- **Meddelande:**`The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Orsak:** Ett fel entitetsnamn anges som målentitet i ett uppslagsfält med flera mål.

- **Rekommendation:** Ange ett giltigt entitetsnamn för uppslagsfältet med flera mål.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Felkod: DynamicsInvalidTypeForMultiTargetLookupField

- **Meddelande:**`The provided target type is not a valid string. Field: '%fieldName;'.`

- **Orsak:** Värdet i målkolumnen är inte en sträng.

- **Rekommendation:** Ange en giltig sträng i målkolumnen för sökning med flera mål.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Felkod: DynamicsFailedToRequetServer

- **Meddelande:**`The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Orsak:** Dynamics-servern kan inte kommas åt, eller så har nätverket problem.

- **Rekommendation:** Mer information finns i nätverksanslutningen eller i Dynamics-serverloggen. Kontakta Dynamics-supporten om du behöver mer hjälp.


### <a name="error-code--dynamicsfailedtoconnect"></a>Felkod: DynamicsFailedToConnect 
 
 - **Meddelande:**`Failed to connect to Dynamics: %message;` 
 

 - **Orsak:** Om du ser i felmeddelandet innebär det att servern kan `Office 365 auth with OAuth failed` ha vissa konfigurationer som inte är kompatibla med OAuth. 
 
 - **Rekommendation**: 
    1. Kontakta Dynamics-supportteamet med det detaljerade felmeddelandet om du behöver hjälp.  
    1. Använd autentisering med tjänstens huvudnamn och du kan läsa den här [artikeln: Exempel: Dynamics online med Azure AD-tjänstens huvudnamn och certifikatautentisering](https://docs.microsoft.com/azure/data-factory/connector-dynamics-crm-office-365#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication). 
 

 - **Orsak:** Om du ser i felmeddelandet innebär det att du antingen anger fel `Unable to retrieve authentication parameters from the serviceUri` DYNAMICS-tjänst-URL eller proxy/brandvägg för att fånga upp trafiken. 
 
 - **Rekommendation**:
    1. Kontrollera att du har lagt till rätt tjänst-URI i den länkade tjänsten. 
    1. Om du använder IR med egen värd kontrollerar du att brandväggen/proxyn inte fångar upp begäranden till Dynamics-servern. 
   
 
 - **Orsak:** Om du `An unsecured or incorrectly secured fault was received from the other party` ser i felmeddelandet innebär det att oväntade svar togs från serversidan. 
 
 - **Rekommendation**: 
    1. Kontrollera att ditt användarnamn och lösenord är korrekta om du använder Office 365-autentisering. 
    1. Kontrollera att du har matat in rätt tjänst-URI. 
    1. Om du använder en regional CRM-URL (URL har ett nummer efter "crm" ska du se till att använda rätt regionsidentifierare.
    1. Kontakta Dynamics-supportteamet om du behöver hjälp. 
 

 - **Orsak:** Om du ser i felmeddelandet innebär det att ditt organisationsnamn är fel eller att du använde fel `No Organizations Found` CRM-regionsidentifierare i tjänst-URL:en. 
 
 - **Rekommendation**: 
    1. Kontrollera att du har matat in rätt tjänst-URI.
    1. Om du använder den regionala CRM-URL:en (URL har ett nummer efter "crm" ska du se till att använda rätt regional identifierare. 
    1. Kontakta Dynamics-supportteamet för hjälp. 

 
 - **Orsak:** Om du `401 Unauthorized` ser och ett AAD-relaterat felmeddelande innebär det att det finns ett problem med tjänstens huvudnamn. 

 - **Rekommendation:** Följ rekommendationer i felmeddelandet för att åtgärda problemet med tjänstens huvudnamn.  
 
 
 - **Orsak:** För andra fel är problemet vanligtvis på serversidan. 

 - **Rekommendation:** Använd [XrmToolBox för](https://www.xrmtoolbox.com/) att upprätta en anslutning. Om felet kvarstår kontaktar du Dynamics-supportteamet för att få hjälp. 
 
 
### <a name="error-code--dynamicsoperationfailed"></a>Felkod: DynamicsOperationFailed 
 
- **Meddelande:**`Dynamics operation failed with error code: %code;, error message: %message;.` 

- **Orsak:** Åtgärden misslyckades på serversidan. 

- **Rekommendation:** Extrahera felkoden för dynamics-åtgärden från felmeddelandet: och läs artikeln Felkoder för `Dynamics operation failed with error code: {code}` [webbtjänster](https://docs.microsoft.com/powerapps/developer/data-platform/org-service/web-service-error-codes) för mer detaljerad information. Du kan kontakta Dynamics-supportteamet om det behövs. 
 
 
### <a name="error-code--dynamicsinvalidfetchxml"></a>Felkod: DynamicsInvalidFetchXml 
  
- **Meddelande:**`The Fetch Xml query specified is invalid.` 

- **Orsak:** Det finns ett fel i hämtnings-XML:en.  

- **Rekommendation:** Åtgärda felet i hämtnings-XML:en. 
 
 
### <a name="error-code--dynamicsmissingkeycolumns"></a>Felkod: DynamicsMissingKeyColumns 
 
- **Meddelande:**`Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **Orsak:** Källdata innehåller inte nyckelkolumnen för entiteten mottagare. 

- **Rekommendation:** Bekräfta att nyckelkolumnerna finns i källdata eller mappa en källkolumn till nyckelkolumnen i mottagarentiteten. 
 
 
### <a name="error-code--dynamicsprimarykeymustbeguid"></a>Felkod: DynamicsPrimaryKeyMustBeGuid 
 
- **Meddelande:**`The primary key attribute '%attribute;' must be of type guid.` 
 
- **Orsak:** Typen av primärnyckelkolumn är inte Guid. 
 
- **Rekommendation:** Kontrollera att den primära nyckelkolumnen i källdata är av typen "Guid". 
 

### <a name="error-code--dynamicsalternatekeynotfound"></a>Felkod: DynamicsAlternateKeyNotFound 
 
- **Meddelande:**`Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **Orsak:** Den angivna alternativa nyckeln finns inte, vilket kan bero på fel nyckelnamn eller otillräcklig behörighet. 
 
- **Rekommendation**: <br/> 
    1. Korrigera stavfel i nyckelnamnet.<br/> 
    1. Kontrollera att du har tillräcklig behörighet för entiteten. 
 
 
### <a name="error-code--dynamicsinvalidschemadefinition"></a>Felkod: DynamicsInvalidSchemaDefinition 
 
- **Meddelande:**`The valid structure information (column name and type) are required for Dynamics source.` 
 
- **Orsak:** Sink columns in the column mapping miss the 'type' property.Cause : Sink columns in the column mapping miss the 'type' property. 
 
- **Rekommendation:** Du kan lägga till egenskapen "type" i kolumnerna i kolumnmappningen med hjälp av JSON-redigeraren i portalen. 


## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Felkod: FtpFailedToConnectToFtpServer

- **Meddelande:**`Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Orsak:** En felaktig länkad tjänsttyp kan användas för FTP-servern, till exempel att använda den länkade tjänsten Säker FTP (SFTP) för att ansluta till en FTP-server.

- **Rekommendation:** Kontrollera målserverns port. FTP använder port 21.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Felkod: HttpFileFailedToRead

- **Meddelande:**`Failed to read data from http server. Check the error from http server：%message;`

- **Orsak:** Det här felet uppstår när Azure Data Factory pratar med HTTP-servern, men HTTP-begäran misslyckas.

- **Rekommendation:** Kontrollera HTTP-statuskoden i felmeddelandet och åtgärda fjärrserverproblemet.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Felkod: ArgumentOutOfRangeException

- **Meddelande:**`Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Orsak:** I Data Factory stöds DateTime-värden i intervallet 0001-01-01 00:00:00 till 9999-12-31 23:59:59. Oracle stöder dock ett större antal DateTime-värden, till exempel BC century eller min/sek>59, vilket leder till fel i Data Factory.

- **Rekommendation**: 

    Om du vill se om värdet i Oracle ligger inom intervallet för Data Factory kör `select dump(<column name>)` du . 

    Information om bytesekvensen i resultatet finns i [Hur lagras datum i Oracle?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle).


## <a name="orc-format"></a>ORC-format

### <a name="error-code-orcjavainvocationexception"></a>Felkod: OrcJavaInvocationException

- **Meddelande:**`An error occurred when invoking Java, message: %javaException;.`
- **Orsaker och rekommendationer:** Olika orsaker kan leda till det här felet. Kontrollera listan nedan för möjliga orsaksanalyser och relaterade rekommendationer.

    | Orsaksanalys                                               | Rekommendation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | När felmeddelandet innehåller strängarna "java.lang.OutOfMemory", "Java heap space" och "doubleCapacity" är det vanligtvis ett minneshanteringsproblem i en gammal version av Integration Runtime. | Om du använder en egen värd Integration Runtime rekommenderar vi att du uppgraderar till den senaste versionen. |
    | När felmeddelandet innehåller strängen "java.lang.OutOfMemory" har inte Integration Runtime tillräckligt med resurser för att bearbeta filerna. | Begränsa de samtidiga körningarna i Integration Runtime. För IR med egen värd skalar du upp till en kraftfull dator med minne som är lika med eller större än 8 GB. |
    |När felmeddelandet innehåller strängen "NullPointerReference" kan orsaken vara ett tillfälligt fel. | Försök att utföra åtgärden igen. Kontakta supporten om problemet kvarstår. |
    | När felmeddelandet innehåller strängen "BufferOverflowException" kan orsaken vara ett tillfälligt fel. | Försök att utföra åtgärden igen. Kontakta supporten om problemet kvarstår. |
    | När felmeddelandet innehåller strängen "java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable kan inte konverteras till org.apache.hadoop.io.Text" kan orsaken vara ett typkonverteringsproblem i Java Runtime. Det innebär vanligtvis att källdata inte kan hanteras väl i Java Runtime. | Det här är ett dataproblem. Försök att använda en sträng i stället för tecken eller varchar i ORC-formatdata. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Felkod: OrcDateTimeExceedLimit

- **Meddelande:**`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Orsak:** Om datetime-värdet är "0001-01-01 00:00:00" kan det bero på skillnaderna mellan [Kalender-kalendern och gregoriansk kalender](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Rekommendation:** Kontrollera tickvärdet och undvik att använda datetime-värdet "0001-01-01 00:00:00".


## <a name="parquet-format"></a>Parquet-format

### <a name="error-code-parquetjavainvocationexception"></a>Felkod: ParquetJavaInvocationException

- **Meddelande:**`An error occurred when invoking java, message: %javaException;.`

- **Orsaker och rekommendationer:** Olika orsaker kan leda till det här felet. Kontrollera listan nedan för möjliga orsaksanalyser och relaterade rekommendationer.

    | Orsaksanalys                                               | Rekommendation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | När felmeddelandet innehåller strängarna "java.lang.OutOfMemory", "Java heap space" och "doubleCapacity" är det vanligtvis ett minneshanteringsproblem i en gammal version av Integration Runtime. | Om du använder en IR med egen värd och versionen är tidigare än 3.20.7159.1 rekommenderar vi att du uppgraderar till den senaste versionen. |
    | När felmeddelandet innehåller strängen "java.lang.OutOfMemory" har inte Integration Runtime tillräckligt med resurser för att bearbeta filerna. | Begränsa de samtidiga körningarna i Integration Runtime. För IR med egen värd skalar du upp till en kraftfull dator med minne som är lika med eller större än 8 GB. |
    | När felmeddelandet innehåller strängen "NullPointerReference" kan det vara ett tillfälligt fel. | Försök att utföra åtgärden igen. Kontakta supporten om problemet kvarstår. |

### <a name="error-code-parquetinvalidfile"></a>Felkod: ParquetInvalidFile

- **Meddelande:**`File is not a valid Parquet file.`

- **Orsak:** Det här är ett parquet-filproblem.

- **Rekommendation:** Kontrollera om indata är en giltig Parquet-fil.


### <a name="error-code-parquetnotsupportedtype"></a>Felkod: ParquetNotSupportedType

- **Meddelande:**`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Orsak:** Parquet-formatet stöds inte i Azure Data Factory.

- **Rekommendation:** Dubbelkolla källdata genom att gå till Filformat och [komprimerings-codec som stöds efter kopieringsaktivitet i Azure Data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Felkod: ParquetMissedDecimalPrecisionScale

- **Meddelande:**`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Orsak:** Antalet precision och skala parsades, men ingen sådan information angavs.

- **Rekommendation:** Källan returnerar inte rätt precision och skalningsinformation. Kontrollera problemkolumnen för att få information.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Felkod: ParquetInvalidDecimalPrecisionScale

- **Meddelande:**`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Orsak:** Schemat är ogiltigt.

- **Rekommendation:** Kontrollera problemkolumnen för precision och skalning.


### <a name="error-code-parquetcolumnnotfound"></a>Felkod: ParquetColumnNotFound

- **Meddelande:**`Column %column; does not exist in Parquet file.`

- **Orsak:** Källschemat är ett matchningsfel med schemat för mottagaren.

- **Rekommendation:** Kontrollera mappningarna i aktiviteten. Kontrollera att källkolumnen kan mappas till rätt mottagare.


### <a name="error-code-parquetinvaliddataformat"></a>Felkod: ParquetInvalidDataFormat

- **Meddelande:**`Incorrect format of %srcValue; for converting to %dstType;.`

- **Orsak:** Data kan inte konverteras till den typ som anges i mappings.source.

- **Rekommendation:** Dubbelkolla källdata eller ange rätt datatyp för den här kolumnen i kolumnmappningen för kopieringsaktiviteten. Mer information finns i Filformat [och komprimerings-codec som stöds genom kopieringsaktivitet i Azure Data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Felkod: ParquetDataCountNotMatchColumnCount

- **Meddelande:**`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Orsak:** Ett matchningsfel mellan antalet källkolumner och antalet mottagare.

- **Rekommendation:** Dubbelkontroll för att se till att antalet källkolumner är samma som antalet mottagare i "mappning".


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Felkod: ParquetDataTypeNotMatchColumnType

- **Meddelande:**`The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Orsak:** Data från källan kan inte konverteras till den typ som har definierats i mottagaren.

- **Rekommendation:** Ange en korrekt typ i mapping.sink.


### <a name="error-code-parquetbridgeinvaliddata"></a>Felkod: ParquetBridgeInvalidData

- **Meddelande:**`%message;`

- **Orsak:** Datavärdet har överskridit gränsen.

- **Rekommendation:** Försök igen. Kontakta oss om problemet kvarstår.


### <a name="error-code-parquetunsupportedinterpretation"></a>Felkod: ParquetUnsupportedInterpretation

- **Meddelande:**`The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Orsak:** Det här scenariot stöds inte.

- **Rekommendation:**"ParquetInterpretFor" ska inte vara "sparkSql".


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Felkod: ParquetUnsupportFileLevelCompressionOption

- **Meddelande:**`File level compression is not supported for Parquet.`

- **Orsak:** Det här scenariot stöds inte.

- **Rekommendation:** Ta bort CompressionType i nyttolasten.


### <a name="error-code-usererrorjniexception"></a>Felkod: UserErrorJniException

- **Meddelande:**`Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Orsak:** det Java Virtual Machine (JVM) kan inte skapas eftersom vissa ogiltiga (globala) argument har angetts.

- **Rekommendation:** Logga in på den dator som är *värd för varje* nod i din IR med egen värd. Kontrollera att systemvariabeln är korrekt inställd enligt följande: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Starta om alla IR-noder och kör sedan pipelinen igen.


### <a name="arithmetic-overflow"></a>Aritmetiskt spill

- **Symptom:** Felmeddelande uppstod när du kopierar Parquet-filer: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Orsak:** För närvarande stöds endast decimalvärdet <= 38 och längden på heltalsdelen <= 20 när du kopierar filer från Oracle till Parquet. 

- **Lösning:** Som en lösning kan du konvertera alla kolumner med det här problemet till VARCHAR2.


### <a name="no-enum-constant"></a>Ingen uppräkningskonstant

- **Symptom:** Felmeddelande uppstod när du kopierar data till Parquet-format: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , eller: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Orsak:** 

    Problemet kan bero på blanksteg eller specialtecken som inte stöds (t.ex.; {} ()\n\t=) i kolumnnamnet eftersom Parquet inte stöder ett sådant format. 

    Till exempel parsar ett kolumnnamn *som contoso(test)* typen inom hakparenteser från [koden](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . Felet uppstår eftersom det inte finns någon sådan "testtyp".

    Om du vill kontrollera vilka typer som stöds går du till webbplatsen för GitHub [apache/parquet-mr.](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)

- **Lösning**: 

    Dubbelkolla för att se om:
    - Det finns blanksteg i mottagarens kolumnnamn.
    - Den första raden med blanksteg används som kolumnnamn.
    - Typen OriginalType stöds. Försök att undvika att använda dessa specialtecken: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Felkod: RestSinkCallFailed

- **Meddelande:**`Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Orsak:** Det här felet uppstår när Azure Data Factory pratar med REST-slutpunkten över HTTP-protokollet och begäran misslyckas.

- **Rekommendation:** Kontrollera HTTP-statuskoden eller meddelandet i felmeddelandet och åtgärda fjärrserverproblemet.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Oväntat nätverkssvar från REST-anslutningsappen

- **Symptom:** Slutpunkten får ibland ett oväntat svar (400, 401, 403, 500) från REST-anslutningsappen.

- **Orsak:** REST-källanslutningen använder URL:en och HTTP-metoden/-huvudet/brödtexten från den länkade tjänsten/datauppsättningen/kopieringskällan som parametrar när den skapar en HTTP-begäran. Problemet beror troligen på några misstag i en eller flera angivna parametrar.

- **Lösning**: 
    - Använd "curl" i kommandotolken för att se om parametern är orsaken (**Accept** och **User-Agent-huvuden** bör alltid inkluderas):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Om kommandot returnerar samma oväntade svar åtgärdar du de föregående parametrarna med "curl" tills det returnerar det förväntade svaret. 

      Du kan också använda "curl --help" för mer avancerad användning av kommandot.

    - Om endast rest Data Factory anslutningen returnerar ett oväntat svar kontaktar du Microsofts support för ytterligare felsökning.
    
    - Observera att "curl" kanske inte lämpar sig för att återskapa ett SSL-certifikatverifieringsproblem. I vissa scenarier kördes curl-kommandot utan problem med SSL-certifikatverifieringen. Men när samma URL körs i en webbläsare returneras inget SSL-certifikat för klienten att upprätta förtroende med servern.

      Verktyg som **Postman** **och Fiddler** rekommenderas i föregående fall.


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Felkod: SftpOperationFail

- **Meddelande:**`Failed to '%operation;'. Check detailed error from SFTP.`

- **Orsak:** Ett problem med SFTP-åtgärden.

- **Rekommendation:** Kontrollera felinformationen från SFTP.


### <a name="error-code-sftprenameoperationfail"></a>Felkod: SftpRenameOperationFail

- **Meddelande:**`Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Orsak:** SFTP-servern stöder inte namnbyte av temp-filen.

- **Rekommendation:** Ange "useTempFileRename" som falskt i kopierings mottagare för att inaktivera uppladdning till temp-filen.


### <a name="error-code-sftpinvalidsftpcredential"></a>Felkod: SftpInvalidSftpCredential

- **Meddelande:**`Invalid SFTP credential provided for '%type;' authentication type.`

- **Orsak:** Innehållet i den privata nyckeln hämtas från Azure-nyckelvalvet eller SDK:n, men det är inte korrekt kodat.

- **Rekommendation**:  

    Om innehållet för den privata nyckeln kommer från ditt nyckelvalv kan den ursprungliga nyckelfilen fungera om du laddar upp den direkt till den länkade SFTP-tjänsten.

    Mer information finns i [Kopiera data från och till SFTP-servern med hjälp av Azure Data Factory](./connector-sftp.md#use-ssh-public-key-authentication). Innehållet i den privata nyckeln är base64-kodat innehåll för privat SSH-nyckel.

    Koda *hela den* ursprungliga privata nyckelfilen med base64-kodning och lagra den kodade strängen i nyckelvalvet. Den ursprungliga privata nyckelfilen är den som fungerar på den länkade SFTP-tjänsten om du väljer **Ladda** upp från filen.

    Här är några exempel som du kan använda för att generera strängen:

    - Använd C#-kod:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Använd Python-kod:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Använd ett base64-konverteringsverktyg från tredje part. Vi rekommenderar [formatverktyget Koda till Base64.](https://www.base64encode.org/)

- **Orsak:** Fel nyckelinnehållsformat har valts.

- **Rekommendation**:  

    Privat SSH-nyckel i PKCS#8-format (börja med "-----BEGIN ENCRYPTED PRIVATE KEY-----") stöds för närvarande inte för åtkomst till SFTP-servern i Data Factory. 

    Om du vill konvertera nyckeln till traditionellt SSH-nyckelformat och börjar med "-----BEGIN RSA PRIVATE KEY-----" kör du följande kommandon:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Orsak:** Ogiltiga autentiseringsuppgifter eller privat nyckelinnehåll.

- **Rekommendation:** Om du vill se om nyckelfilen eller lösenordet är korrekt kontrollerar du med verktyg som WinSCP.

### <a name="sftp-copy-activity-failed"></a>SFTP-kopieringsaktiviteten misslyckades

- **Symptom:** 
  * Felkod: UserErrorInvalidColumnMappingColumnNotFound 
  * Felmeddelande: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Orsak:** Källan innehåller inte en kolumn med namnet "AccMngr".

- **Lösning:** Kontrollera datauppsättningskonfigurationen genom att mappa måldatauppsättningskolumnen för att avgöra om kolumnen "AccMngr" finns.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Felkod: SftpFailedToConnectToSftpServer

- **Meddelande:**`Failed to connect to SFTP server '%server;'.`

- **Orsak:** Om felmeddelandet innehåller strängen "Socket read operation has time out after 30,000 milliseconds", är en möjlig orsak att en felaktig länkad tjänsttyp används för SFTP-servern. Du kan till exempel använda den länkade FTP-tjänsten för att ansluta till SFTP-servern.

- **Rekommendation:** Kontrollera målserverns port. Som standard använder SFTP port 22.

- **Orsak:** Om felmeddelandet innehåller strängen "Serversvaret innehåller inte SSH-protokollidentifiering" är en möjlig orsak att SFTP-servern begränsade anslutningen. Data Factory flera anslutningar att ladda ned från SFTP-servern parallellt, och ibland kan det uppstå SFTP-serverbegränsning. Normalt returnerar olika servrar olika fel när de stöter på begränsning.

- **Rekommendation**:  

    Ange det maximala antalet samtidiga anslutningar för SFTP-datauppsättningen som 1 och kör kopieringsaktiviteten igen. Om aktiviteten lyckas kan du vara säker på att begränsning är orsaken.

    Om du vill höja upp det låga dataflödet kontaktar du SFTP-administratören för att öka gränsen för antalet samtidiga anslutningar, eller så kan du göra något av följande:

    * Om du använder IR med egen värd lägger du till IR-datorns IP-adress med egen värd i listan över tillåtna.
    * Om du använder en Azure IR lägger du till [Azure Integration Runtime IP-adresser](./azure-integration-runtime-ip-addresses.md). Om du inte vill lägga till ett intervall med IP-adresser i LISTAN över tillåtna SFTP-servrar använder du IR med egen värd i stället.

## <a name="sharepoint-online-list"></a>SharePoint Online-lista

### <a name="error-code-sharepointonlineauthfailed"></a>Felkod: SharePointOnlineAuthFailed

- **Meddelande:**`The access token generated failed, status code: %code;, error message: %message;.`

- **Orsak:** Id och nyckel för tjänstens huvudnamn kanske inte har angetts korrekt.

- **Rekommendation:** Kontrollera ditt registrerade program (ID för tjänstens huvudnamn) och nyckeln för att se om de är korrekt inställda.


## <a name="xml-format"></a>XML-format

### <a name="error-code-xmlsinknotsupported"></a>Felkod: XmlSinkNotSupported

- **Meddelande:**`Write data in XML format is not supported yet, choose a different format!`

- **Orsak:** En XML-datauppsättning användes som en datauppsättning för mottagare i din kopieringsaktivitet.

- **Rekommendation:** Använd en datauppsättning i ett annat format än för datauppsättningen för mottagare.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Felkod: XmlAttributeColumnNameConflict

- **Meddelande:**`Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Orsak:** Ett attributprefix användes, vilket orsakade konflikten.

- **Rekommendation:** Ange ett annat värde för egenskapen "attributePrefix".


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Felkod: XmlValueColumnNameConflict

- **Meddelande:**`Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Orsak:** Ett av de underordnade elementnamnen användes som kolumnnamn för elementvärdet.

- **Rekommendation:** Ange ett annat värde för egenskapen "valueColumn".


### <a name="error-code-xmlinvalid"></a>Felkod: XmlInvalid

- **Meddelande:**`Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Orsak:** XML-indatafilen är inte väl utformad.

- **Rekommendation:** Korrigera XML-filen så att den blir välformad.


## <a name="general-copy-activity-error"></a>Allmänt kopieringsaktivitetsfel

### <a name="error-code-jrenotfound"></a>Felkod: JreNotFound

- **Meddelande:**`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Orsak:** IR med egen värd kan inte hitta Java Runtime. Java Runtime krävs för läsning av specifika källor.

- **Rekommendation:** Kontrollera din Integration [Runtime-miljö. Mer information finns i Använda Integration Runtime](./format-parquet.md#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Felkod: JokerteckenPathSinkNotSupported

- **Meddelande:**`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Orsak:** Datauppsättningen för mottagare stöder inte jokerteckenvärden.

- **Rekommendation:** Kontrollera datauppsättningen för mottagare och skriva om sökvägen utan att använda ett jokerteckenvärde.


### <a name="fips-issue"></a>FIPS-problem

- **Symptom:** aktiviteten Kopiera misslyckas på en FIPS-aktiverad IR-dator med egen värd med följande felmeddelande: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Orsak:** Det här felet kan inträffa när du kopierar data med anslutningsappar som Azure Blob, SFTP och så vidare. FIPS (Federal Information Processing Standards) definierar en viss uppsättning kryptografiska algoritmer som får användas. När FIPS-läge är aktiverat på datorn blockeras vissa kryptografiska klasser som kopieringsaktiviteten är beroende av i vissa scenarier.

- **Lösning:** Lär dig varför vi inte rekommenderar ["FIPS-läge"](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)längre och utvärdera om du kan inaktivera FIPS på din lokala IR-dator.

    Om du bara vill låta Azure Data Factory FIPS och göra så att aktivitetskörningarna lyckas gör du följande:

    1. Öppna mappen där IR med egen värd är installerad. Sökvägen är vanligtvis *C:\Program Files\Microsoft Integration Runtime \<IR version> \Shared*.

    2. Öppna *diawp.exe.config* och lägg sedan till i slutet av `<runtime>` avsnittet enligt `<enforceFIPSPolicy enabled="false"/>` följande:

        ![Skärmbild av ett avsnitt i filen diawp.exe.config som visar FIPS inaktiverat.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Spara filen och starta sedan om den lokala IR-datorn.


## <a name="next-steps"></a>Nästa steg

Om du behöver mer felsökningshjälp kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktionsbegäranden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A-sida](/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)