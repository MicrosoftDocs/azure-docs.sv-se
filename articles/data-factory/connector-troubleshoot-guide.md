---
title: Felsöka Azure Data Factory-anslutningsprogram
description: Lär dig hur du felsöker anslutnings problem i Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: e6591762ed6a7e2b462a209730276f3198d86ae8
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821476"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Felsöka Azure Data Factory-anslutningsprogram

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln visar vanliga fel söknings metoder för anslutningar i Azure Data Factory.
  
## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Felkod: AzureBlobOperationFailed

- **Meddelande**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Orsak**: problem med Blob Storage-åtgärd.

- **Rekommendation**: Mer information finns i fel meddelandet. Läs mer i hjälp dokumentet för BLOB: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes . Kontakta Storage-teamet om du behöver hjälp.


### <a name="invalid-property-during-copy-activity"></a>Ogiltig egenskap vid kopierings aktivitet

- **Meddelande**: `Copy activity <Activity Name> has an invalid "source" property. The source type is not compatible with the dataset <Dataset Name> and its linked service <Linked Service Name>. Please verify your input against.`

- **Orsak**: den typ som definierats i data uppsättningen stämmer inte med den typ av källa/mottagare som definierats i kopierings aktiviteten.

- **Lösning**: redigera data uppsättningen eller pipeline-JSON-definitionen för att göra typerna konsekventa och kör om distributionen.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Fel meddelande: den begärda storleken är för stor

- **Symptom**: du kopierar data till Azure Cosmos dB med standard storleken för Skriv journal och felet *"**begär ande storleken är för stor**"*.

- **Orsak**: Cosmos DB begränsar storleken på en enskild begäran till 2 MB. Formeln är en begär ande storlek = enskild dokument storlek * Skriv batchstorleken. Om din dokument storlek är stor resulterar standard beteendet i för stor storlek på begäran. Du kan finjustera Skriv batchstorleken.

- **Lösning**: minska värdet för "Skriv batchstorlek" i kopians aktivitets mottagare (Standardvärdet är 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Fel meddelande: överträdelse av unikhetsvillkor för unikt index

- **Symptom**: när du kopierar data till Cosmos DB visas följande fel:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Orsak**: det finns två möjliga orsaker:

    - Om du använder **Infoga** som Skriv-beteende innebär det här felet att källdata har rader/objekt med samma ID.
    - Om du använder **upsert** som Skriv beteende och anger en annan unik nyckel till behållaren, innebär det här felet att källdata har rader/objekt med olika ID: n men samma värde för den definierade unika nyckeln.

- **Lösning**: 

    - Ange **upsert** som Skriv-beteende för cause1.
    - För orsak 2, se till att varje dokument har ett annat värde för definierad unik nyckel.

### <a name="error-message-request-rate-is-large"></a>Fel meddelande: den begärda frekvensen är stor

- **Symptom**: när du kopierar data till Cosmos DB visas följande fel:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Orsak**: de begärda enheterna som används är större än den tillgängliga ru som kon figurer ats i Cosmos dB. Lär dig hur Cosmos DB beräknar RU [härifrån.](../cosmos-db/request-units.md#request-unit-considerations)

- **Lösning**: här är två lösningar:

    - **Öka värdet för containern ru** till större i Cosmos DB, vilket förbättrar prestandan för kopierings aktiviteten, men kostar mer på Cosmos dB. 
    - Minska **writeBatchSize** till ett mindre värde (till exempel 1000) och ange **parallelCopies** till ett mindre värde, till exempel 1, vilket gör att kopieringen körs sämre än vad som är aktuellt men inte kostar mer kostnad i Cosmos dB.

### <a name="column-missing-in-column-mapping"></a>Kolumn saknas i kolumn mappning

- **Symptom**: när du importerar schema för Cosmos dB för kolumn mappning saknas vissa kolumner. 

- **Orsak**: ADF härleder schemat från de första 10 Cosmos DB dokumenten. Om vissa kolumner/egenskaper inte har något värde i dessa dokument, upptäcks de inte av ADF, vilket innebär att de inte kan visas.

- **Lösning**: du kan justera frågan enligt nedan om du vill att kolumnen ska visas i resultat uppsättningen med tomt värde: (Antag: kolumnen "omöjlig" saknas i de första 10 dokumenten). Alternativt kan du lägga till kolumnen manuellt för mappning.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Fel meddelande: GuidRepresentation för läsaren är CSharpLegacy

- **Symptom**: när du kopierar data från Cosmos DB MongoAPI/MONGODB med uuid-fältet, så skulle du trycka på följande fel:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Orsak**: det finns två sätt att representera UUID i bson-UuidStardard och UuidLegacy. Som standard används UuidLegacy för att läsa data. Du kommer att trycka på fel om dina UUID-data i MongoDB är UuidStandard.

- **Lösning**: Lägg till alternativet "**uuidRepresentation = standard**" i MongoDB anslutnings sträng. Mer information finns i [anslutnings strängen för MongoDB](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code--cosmosdbsqlapioperationfailed"></a>Felkod: CosmosDbSqlApiOperationFailed

- **Meddelande**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Orsak**: problem med CosmosDbSqlApi-åtgärd.

- **Rekommendation**: Mer information finns i fel meddelandet. Läs mer i [CosmosDb-hjälp dokument](https://docs.microsoft.com/azure/cosmos-db/troubleshoot-dot-net-sdk). Kontakta CosmosDb-teamet om du behöver hjälp.


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Fel meddelande: den underliggande anslutningen stängdes: det gick inte att upprätta en förtroende relation för den säkra SSL/TLS-kanalen.

- **Symptom**: kopierings aktiviteten Miss lyckas med följande fel: 

    ```
    Message: ErrorCode = `UserErrorFailedFileOperation`, Error Message = `The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`.
    ```

- **Orsak**: certifikat valideringen misslyckades under TLS-handskakning.

- **Lösning**: lösning: Använd mellanlagrad kopia för att hoppa över TLS-valideringen för ADLS gen1. Du måste återskapa det här problemet och samla in Netmon-spårning och sedan engagera nätverks teamet för att kontrol lera den lokala nätverks konfigurationen.

    ![Felsöka ADLS Gen1](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Fel meddelande: fjärrservern returnerade ett fel: (403) ej tillåtet

- **Symptom**: kopierings aktiviteten fungerar inte med följande fel: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Orsak**: en möjlig orsak är att tjänstens huvud namn eller hanterade identitet som du använder inte har behörighet att komma åt en viss mapp/fil.

- **Lösning**: bevilja motsvarande behörigheter för alla mappar och undermappar som du behöver kopiera. Referera till [det här dokumentet](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Fel meddelande: det gick inte att hämta åtkomsttoken med hjälp av tjänstens huvud namn. ADAL-fel: service_unavailable

- **Symptom**: kopierings aktiviteten fungerar inte med följande fel:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Orsak**: när den server (STS) som ägs av Azure Active Directory inte är tillgänglig, så returnerar den ett HTTP-fel 503 om den är upptagen för att hantera begär Anden. 

- **Lösning**: kör kopierings aktiviteten igen om några minuter.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Felkod: ADLSGen2OperationFailed

- **Meddelande**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Orsak**: ADLS Gen2 returnerar fel som indikerar att åtgärden misslyckades.

- **Rekommendation**: kontrol lera det detaljerade fel meddelandet som har utlösts av ADLS Gen2. Försök igen om det orsakas av ett tillfälligt fel. Om du behöver ytterligare hjälp kontaktar du Azure Storage support och anger ID för begäran i fel meddelande.

- **Orsak**: när fel meddelandet innehåller "förbjudet" kanske tjänstens huvud namn eller hanterade identitet inte har tillräcklig behörighet för att komma åt ADLS Gen2.

- **Rekommendation**: se hjälp dokumentet: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication .

- **Orsak**: när fel meddelandet innehåller "InternalServerError" returneras felet av ADLS Gen2.

- **Rekommendation**: det kan bero på ett tillfälligt fel, försök igen. Om problemet kvarstår kontaktar du Azure Storage support och anger ID för begäran i fel meddelande.

### <a name="request-to-adls-gen2-account-met-timeout-error"></a>Begäran till ADLS Gen2 konto uppfyllt tids gräns fel

- **Meddelande**: felkod = `UserErrorFailedBlobFSOperation` , fel meddelande = `BlobFS operation failed for: A task was canceled` .

- **Orsak**: problemet orsakas av timeout-felet för ADLS Gen2 Sink, som oftast inträffar på den lokala IR-datorn.

- **Rekommendation**: 

    - Placera din egen IR-dator och mål ADLS Gen2 konto i samma region om det är möjligt. Detta kan undvika slumpmässiga tids gräns fel och få bättre prestanda.

    - Kontrol lera om det finns någon speciell nätverks inställning som ExpressRoute och se till att nätverket har tillräckligt med bandbredd. Vi rekommenderar att du sänker inställningen för IR-arbetsjobb med egen värd när den totala bandbredden är låg, genom vilken kan undvika nätverks resurs konkurrens mellan flera samtidiga jobb.

    - Använd mindre block storlek för icke-binär kopia för att minimera tids gräns felet om fil storleken är måttlig eller liten. Referera till [Blob Storage spärra block](https://docs.microsoft.com/rest/api/storageservices/put-block).

       Om du vill ange den anpassade block storleken kan du redigera egenskapen i. JSON-redigeraren:
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-file-storage"></a>Azure File Storage

### <a name="error-code--azurefileoperationfailed"></a>Felkod: AzureFileOperationFailed

- **Meddelande**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Orsak**: problem med Azure File Storage-åtgärden.

- **Rekommendation**: Mer information finns i fel meddelandet. Se hjälp dokument för Azure File: https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes . Kontakta lagrings teamet om du behöver hjälp.


## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure Synapse-analys/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Felkod: SqlFailedToConnect

- **Meddelande**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Orsak**: Azure SQL: om fel meddelandet innehåller "SqlErrorNumber = 47073", innebär det att offentlig nätverks åtkomst nekas i anslutnings inställningen.

- **Rekommendation**: ange alternativet neka offentlig nätverks åtkomst i Azure SQL-brandväggen till Nej. Läs mer från https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access .

- **Orsak**: Azure SQL: om fel meddelandet innehåller SQL-felkod, t. ex. "SqlErrorNumber = [ErrorCode]", se fel söknings guide för Azure SQL.

- **Rekommendation**: Läs mer från https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues .

- **Orsak**: kontrol lera om port 1433 finns i listan över tillåtna brand Väggs listor.

- **Rekommendation**: Följ med följande referens dokument: https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by- .

- **Orsak**: om fel meddelandet innehåller "SqlException", genererar SQL Database det fel som indikerar att en viss åtgärd misslyckades.

- **Rekommendation**: Sök efter SQL-felkod i detta referens dokument för mer information: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Om du behöver ytterligare hjälp kan du kontakta Azure SQL-supporten.

- **Orsak**: om det här är ett tillfälligt problem (t. ex. en instabil nätverks anslutning) kan du lägga till nya försök i aktivitets principen för att minimera.

- **Rekommendation**: Följ det här referens dokumentet: https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy .

- **Orsak**: om fel meddelandet innehåller "klient med IP-adress"... " har inte behörighet att komma åt servern och du försöker ansluta till Azure SQL Database, vanligt vis orsakas det av Azure SQL Database brand Väggs problem.

- **Rekommendation**: Aktivera alternativet Tillåt Azure-tjänster och-resurser för att få åtkomst till den här servern i Azure SQL Server brand Väggs konfiguration. Referens dokument: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure .


### <a name="error-code--sqloperationfailed"></a>Felkod: SqlOperationFailed

- **Meddelande**: `A database operation failed. Please search error to get more details.`

- **Orsak**: om fel meddelandet innehåller "SqlException", genererar SQL Database det fel som indikerar att en viss åtgärd misslyckades.

- **Rekommendation**: om SQL-fel inte är klart kan du försöka ändra databasen till den senaste kompatibilitetsnivån "150". Den kan utlösa de senaste versionerna av SQL-fel. Referera till [informations dokumentet](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Information om hur du felsöker SQL-problem finns i SQL-felkoden i det här referens dokumentet: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Om du behöver ytterligare hjälp kan du kontakta Azure SQL-supporten.

- **Orsak**: om fel meddelandet innehåller "PdwManagedToNativeInteropException", orsakas det vanligt vis av matchnings fel mellan käll-och mottagar kolumn storlekarna.

- **Rekommendation**: kontrol lera storleken på kolumnerna källa och mottagare. Om du behöver ytterligare hjälp kan du kontakta Azure SQL-supporten.

- **Orsak**: om fel meddelandet innehåller "InvalidOperationException", orsakas det vanligt vis av ogiltiga indata.

- **Rekommendation**: om du vill identifiera vilken rad som stöter på problemet aktiverar du fel tolerans funktionen för kopierings aktiviteten, som kan dirigera om problematiska rader till lagringen för ytterligare undersökning. Referens dokument: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlunauthorizedaccess"></a>Felkod: SqlUnauthorizedAccess

- **Meddelande**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Orsak**: autentiseringsuppgiften är felaktigt eller också har inloggnings kontot inte åtkomst till SQL Database.

- **Rekommendation**: kontrol lera att inloggnings kontot har tillräcklig behörighet för att komma åt SQL Database.


### <a name="error-code--sqlopenconnectiontimeout"></a>Felkod: SqlOpenConnectionTimeout

- **Meddelande**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Orsak**: det kan vara SQL Database ett tillfälligt fel.

- **Rekommendation**: försök att uppdatera länkad anslutnings sträng för länkad tjänst med större tids gräns värde.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Felkod: SqlAutoCreateTableTypeMapFailed

- **Meddelande**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Orsak**: tabellen för automatisk generering kan inte uppfylla käll kraven.

- **Rekommendation**: uppdatera kolumn typen i "mappningar" eller skapa tabellen mottagare manuellt i mål servern.


### <a name="error-code--sqldatatypenotsupported"></a>Felkod: SqlDataTypeNotSupported

- **Meddelande**: `A database operation failed. Check the SQL errors.`

- **Orsak**: om problemet uppstår på SQL-källan och felet är relaterat till SqlDateTime overflow är datavärdet över logik typ intervallet (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM).

- **Rekommendation**: omvandla typen till sträng i SQL-frågan för källan eller ändra kolumn typen till string i kolumn mappningen för kopiera aktivitet.

- **Orsak**: om problemet inträffar på SQL-Sink och felet är relaterat till SqlDateTime overflow är datavärdet över det tillåtna intervallet i tabellen Sink.

- **Rekommendation**: uppdatera motsvarande kolumn typ till ' datetime2 '-typ i tabellen Sink.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Felkod: SqlInvalidDbStoredProcedure

- **Meddelande**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Orsak**: den angivna lagrade proceduren är inte giltig. Det kan bero på att den lagrade proceduren inte returnerar några data.

- **Rekommendation**: verifiera den lagrade proceduren med SQL-verktyg. Se till att den lagrade proceduren kan returnera data.


### <a name="error-code--sqlinvaliddbquerystring"></a>Felkod: SqlInvalidDbQueryString

- **Meddelande**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Orsak**: den angivna SQL-frågan är inte giltig. Det kan bero på att frågan inte returnerar några data

- **Rekommendation**: validera SQL-frågan av SQL-verktyg. Kontrol lera att frågan kan returnera data.


### <a name="error-code--sqlinvalidcolumnname"></a>Felkod: SqlInvalidColumnName

- **Meddelande**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Orsak**: det går inte att hitta kolumnen. En möjlig konfiguration är felaktig.

- **Rekommendation**: kontrol lera kolumnen i frågan, "struktur" i data uppsättningen och "mappningar" i aktiviteten.


### <a name="error-code--sqlbatchwritetimeout"></a>Felkod: SqlBatchWriteTimeout

- **Meddelande**: `Timeouts in SQL write operation.`

- **Orsak**: det kan vara SQL Database ett tillfälligt fel.

- **Rekommendation**: försök igen. Kontakta Azure SQL-supporten om problemet återskapnings.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Felkod: SqlBatchWriteTransactionFailed

- **Meddelande**: `SQL transaction commits failed`

- **Orsak**: om undantags informationen kontinuerligt meddelar Transaktionstimeout är nätverks fördröjningen mellan integration Runtime och Database högre än standard tröskelvärdet som 30 sekunder.

- **Rekommendation**: uppdatera SQL Linked service-anslutningssträngen med värdet för anslutningstimeout är lika med 120 eller högre och kör om aktiviteten.

- **Orsak**: om undantags informationen tillfälligt säger att SQLConnection är bruten kan det vara tillfälligt nätverks fel eller problem med SQL Database Sidan

- **Rekommendation**: försök utföra aktiviteten igen och granska SQL Database sidans mått.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Felkod: SqlBulkCopyInvalidColumnLength

- **Meddelande**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Orsak**: SQL Mass kopiering misslyckades på grund av att en ogiltig kolumn längd togs emot från BCP-klienten.

- **Rekommendation**: om du vill identifiera vilken rad som stöter på problemet aktiverar du fel tolerans funktionen för kopierings aktiviteten, som kan dirigera om problematiska rader till lagringen för ytterligare undersökning. Referens dokument: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlconnectionisclosed"></a>Felkod: SqlConnectionIsClosed

- **Meddelande**: `The connection is closed by SQL Database.`

- **Orsak**: SQL-anslutningen stängs av SQL Database när hög samtidiga körningar och servern avslutar anslutningen.

- **Rekommendation**: fjärrservern STÄNGde SQL-anslutningen. Försök igen. Kontakta Azure SQL-supporten om problemet återskapnings.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Fel meddelande: konverteringen misslyckades vid konvertering från en tecken sträng till uniqueidentifier

- **Symptom**: när du kopierar data från tabell data källa (till exempel SQL Server) till Azure Synapse Analytics med hjälp av mellanlagrad kopiering och PolyBase, träffas följande fel:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Orsak**: Azure Synapse Analytics PolyBase kan inte konvertera en tom sträng till GUID.

- **Lösning**: ange alternativet **Använd typ standard** som falskt under PolyBase Settings i kopierings aktivitets handfat.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Fel meddelande: förväntad datatyp: DECIMAL (x, x), felaktigt värde

- **Symptom**: när du kopierar data från tabell data källa (till exempel SQL Server) till Azure Synapse Analytics med hjälp av mellanlagrad kopiering och PolyBase, träffas följande fel:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Orsak**: Azure Synapse Analytics PolyBase kan inte infoga en tom sträng (null-värde) i decimal kolumnen.

- **Lösning**: ange alternativet **Använd typ standard** som falskt under PolyBase Settings i kopierings aktivitets handfat.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Fel meddelande: Java-undantags meddelande: HdfsBridge:: CreateRecordReader

- **Symptom**: du kopierar data till Azure Synapse Analytics med PolyBase och trycker på följande fel:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Orsak**: den möjliga orsaken är att schemat (total kolumn bredd) är för stort (större än 1 MB). Kontrol lera schemat för Azure Synapse Analytics-tabellen genom att lägga till storleken på alla kolumner:

    - Int-> 4 byte
    - Bigint-> 8 byte
    - Varchar (n), Char (n), binär (n), varbinary (n)-> n byte
    - Nvarchar (n), nchar (n)-> n * 2 byte
    - Datum-> 6 byte
    - Datetime/(2), smalldatetime-> 16 byte
    - DateTimeOffset-> 20 byte
    - Decimal-> 19 byte
    - Float-> 8 byte
    - Money-> 8 byte
    - Smallmoney-> 4 byte
    - Real-> 4 byte
    - Smallint – > 2 byte
    - Time-> 12 byte
    - Tinyint – > 1 byte

- **Lösning**: 
    - Minska kolumn bredden till mindre än 1 MB.
    - Eller använd bulk insert-metoden genom att inaktivera PolyBase.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Fel meddelande: villkoret som anges med HTTP-villkorliga huvud (a) är inte uppfyllt

- **Symptom**: du använder SQL-fråga för att hämta data från Azure Synapse Analytics och följande fel meddelande visas:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Orsak**: problem med Azure Synapse Analytics-träffen som frågar den externa tabellen i Azure Storage.

- **Lösning**: kör samma fråga i SSMS och kontrol lera om samma resultat visas. Om ja, öppna ett support ärende till Azure Synapse Analytics och ange din Azure Synapse Analytics-Server och databas namn för ytterligare fel sökning.
            

### <a name="low-performance-when-load-data-into-azure-sql"></a>Låg prestanda vid inläsning av data i Azure SQL

- **Symptom**: kopiering av data i Azure SQL blir långsam.

- **Orsak**: den grundläggande orsaken till problemet utlöses oftast av flask halsen för Azure SQL-sidan. Här följer några möjliga orsaker:

    - Azure DB-nivån är inte tillräckligt hög.

    - Användning av Azure DB DTU är nära 100%. Du kan [övervaka prestanda](https://docs.microsoft.com/azure/azure-sql/database/monitor-tune-overview) och överväga att uppgradera DB-nivån.

    - Indexen har inte angetts korrekt. Ta bort alla index innan data inläsningen och återskapa dem när belastningen är klar.

    - WriteBatchSize är inte tillräckligt stor för att rymma schema rad storleken. Försök att förstora egenskapen för problemet.

    - I stället för Mass indrag används den lagrade proceduren, vilket förväntas ha sämre prestanda. 

- **Lösning**: se TSG för [kopierings aktivitetens prestanda](https://docs.microsoft.com/azure/data-factory/copy-activity-performance-troubleshooting)


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Prestanda nivån är låg och leads kan inte kopieras

- **Symptom**: nedan visas ett fel meddelande när data kopierades till Azure SQL: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Orsak**: Azure SQL S1 används, vilket träffar i/o-gränser i detta fall.

- **Lösning**: uppgradera prestanda nivån för Azure SQL för att åtgärda problemet. 


### <a name="sql-table-cannot-be-found"></a>Det går inte att hitta SQL-tabellen 

- **Symptom**: ett fel inträffade vid kopiering av data från hybrid till lokal SQL Server tabell:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Orsak**: det aktuella SQL-kontot har inte tillräcklig behörighet för att köra förfrågningar som utfärdats av .net SqlBulkCopy. WriteToServer.

- **Lösning**: växla till ett mer PRIVILEGIE rad SQL-konto.


### <a name="error-message-string-or-binary-data-would-be-truncated"></a>Fel meddelande: sträng data eller binära data skulle trunkeras

- **Symptom**: ett fel uppstod när data kopierades till lokal/Azure SQL Server tabell: 

- **Orsak**: CX SQL Table schema definition har en eller flera kolumner med kortare längd än förväntat.

- **Lösning**: prova följande steg för att åtgärda problemet:

    1. Använd [fel tolerans](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)för SQL-Sink, särskilt "redirectIncompatibleRowSettings" för att felsöka vilka rader som har problemet.

        > [!NOTE]
        > Observera att fel toleransen kan medföra ytterligare körnings tid, vilket kan leda till högre kostnad.

    2. Markera kryss rutan Omdirigerad data med SQL Table schema kolumn längd för att se vilka kolumner som behöver uppdateras.

    3. Uppdatera tabell schema enligt detta.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code--azuretableduplicatecolumnsfromsource"></a>Felkod: AzureTableDuplicateColumnsFromSource

- **Meddelande**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink`

- **Orsak**: det kan vara vanligt för SQL-frågor med kopplade eller ostrukturerade CSV-filer

- **Rekommendation**: dubbel kontrol lera käll kolumnerna och åtgärda detta.


## <a name="db2"></a>DB2

### <a name="error-code--db2driverrunfailed"></a>Felkod: DB2DriverRunFailed

- **Meddelande**: `Error thrown from driver. Sql code: '%code;'`

- **Orsak**: om fel meddelandet innehåller "SQLSTATE = 51002 SQLCODE =-805", se tipset i det här dokumentet: https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties

- **Rekommendation**: försök att ange "NULLID" i egenskapen "packageCollection"


## <a name="delimited-text-format"></a>Avgränsat text format

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Felkod: DelimitedTextColumnNameNotAllowNull

- **Meddelande**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Orsak**: den första raden kommer att användas som kolumn namn när du anger firstRowAsHeader i aktivitet. Det här felet innebär att den första raden innehåller ett tomt värde. Exempel: "Kolumna, ColumnB".

- **Rekommendation**: kontrol lera den första raden och korrigera värdet om det finns ett tomt värde.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Felkod: DelimitedTextMoreColumnsThanDefined

- **Meddelande**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Orsak**: den problematiska radens kolumn antal är större än den första radens kolumn antal. Det kan bero på data problem eller felaktiga inställningar för kolumn avgränsare/citat tecken.

- **Rekommendation**: Hämta rad antalet i fel meddelandet, kontrol lera radens kolumn och korrigera data.

- **Orsak**: om det förväntade kolumn antalet är "1" i fel meddelandet kanske du har angett fel komprimerings-eller format inställningar. ADF parsade därför fil (er) felaktigt.

- **Rekommendation**: kontrol lera format inställningarna för att se till att de matchar dina källfiler.

- **Orsak**: om källan är en mapp är det möjligt att filerna i den angivna mappen har olika scheman.

- **Rekommendation**: kontrol lera att filerna i den aktuella mappen har samma schema.


## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Felkod: DynamicsCreateServiceClientError

- **Meddelande**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Orsak**: det här är ett tillfälligt problem på Dynamics Server-sidan.

- **Rekommendation**: kör pipelinen igen. Försök att minska parallellitet om du inte kan fortsätta. Kontakta Dynamics support om det fortfarande inte fungerar.


### <a name="columns-are-missing-when-previewingimporting-schema"></a>Kolumner saknas vid för hands visning/import av schema

- **Symptom**: några av kolumnerna visar sig att saknas när du importerar schema eller för hands Grans kar data. Fel meddelande: `The valid structure information (column name and type) are required for Dynamics source.`

- **Orsak**: det här problemet är i princip design, eftersom ADF inte kan visa kolumner som inte har något värde i de första 10 posterna. Kontrol lera att de kolumner som du har lagt till har rätt format. 

- **Rekommendation**: Lägg till kolumner på fliken mappning manuellt.


### <a name="error-code--dynamicsmissingtargetformultitargetlookupfield"></a>Felkod: DynamicsMissingTargetForMultiTargetLookupField

- **Meddelande**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Orsak**: mål kolumnen finns inte i källan eller i kolumn mappningen.

- **Rekommendation**: 1. Se till att källan innehåller mål kolumnen. 2. Lägg till mål kolumnen i kolumn mappningen. Se till att kolumnen Sink är i mönstret {fieldName} @EntityReference .


### <a name="error-code--dynamicsinvalidtargetformultitargetlookupfield"></a>Felkod: DynamicsInvalidTargetForMultiTargetLookupField

- **Meddelande**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;"`

- **Orsak**: fel enhets namn anges som målentiteten för ett söknings fält i flera mål.

- **Rekommendation**: Ange ett giltigt entitetsnamn för uppslags fältet för flera mål.


### <a name="error-code--dynamicsinvalidtypeformultitargetlookupfield"></a>Felkod: DynamicsInvalidTypeForMultiTargetLookupField

- **Meddelande**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Orsak**: värdet i mål kolumnen är inte en sträng

- **Rekommendation**: Ange en giltig sträng i kolumnen för söknings mål i flera mål.


### <a name="error-code--dynamicsfailedtorequetserver"></a>Felkod: DynamicsFailedToRequetServer

- **Meddelande**: `The dynamics server or the network is experiencing issues. Check network connectivity or check dynamics server log for more details.`

- **Orsak**: Dynamics-servern är instabil eller otillgänglig eller också har nätverket problem.

- **Rekommendation**: kontrol lera nätverks anslutningen eller kontrol lera i Dynamics Server-loggen om du vill ha mer information. Kontakta Dynamics support om du vill ha mer hjälp.


## <a name="excel-format"></a>Excel-format

### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>Tids gräns eller dåliga prestanda vid parsning av stor Excel-fil

- **Symptom**:

    - När du skapar Excel-datauppsättningen och importera schema från anslutning/butik, förhandsgranska data, lista eller uppdatera kalkyl blad, kan du trycka på timeout-fel om Excel-filen är stor i storlek.

    - När du använder kopierings aktivitet för att kopiera data från en stor Excel-fil (>= 100 MB) till ett annat data lager kan det uppstå problem med långsam prestanda eller OOM.

- **Orsak**: 

    - För åtgärder som att importera schema, för hands Visa data och lista kalkyl blad på Excel-datauppsättning, är timeout-värdet 100 s och static. För stor Excel-fil kan de här åtgärderna inte slutföras inom timeout-värdet.

    - ADF Copy-aktiviteten läser hela Excel-filen i minnet och letar sedan upp det angivna kalkyl bladet och cellerna för att läsa data. Detta beror på att de underliggande SDK: er för SDK används.

- **Lösning**: 

    - När du importerar schema kan du generera en mindre exempel fil, som är en del av original filen och välja importera schema från exempel fil i stället för "Importera schema från anslutning/butik".

    - För List kalkyl bladet, i list rutan kalkyl blad, kan du klicka på "redigera" och ange bladets namn/index i stället.

    - Om du vill kopiera en stor Excel-fil (>100 MB) till en annan lagrings plats kan du använda Excel-källan Data Flow som sport-direktuppspelning och fungerar bättre.
    

## <a name="ftp"></a>FTP

### <a name="error-code--ftpfailedtoconnecttoftpserver"></a>Felkod: FtpFailedToConnectToFtpServer

- **Meddelande**: `Failed to connect to FTP server. Please make sure the provided server informantion is correct, and try again.`

- **Orsak**: felaktig länkad tjänst typ kan användas för FTP-servern, som att använda SFTP-länkad tjänst för att ansluta till en FTP-server.

- **Rekommendation**: kontrol lera mål serverns port. Som standard använder FTP port 21.


## <a name="http"></a>Http

### <a name="error-code--httpfilefailedtoread"></a>Felkod: HttpFileFailedToRead

- **Meddelande**: `Failed to read data from http server. Check the error from http server：%message;`

- **Orsak**: det här felet uppstår när Azure Data Factory prata till http-servern, men åtgärden för http-begäran misslyckades.

- **Rekommendation**: kontrol lera HTTP-statuskoden \ meddelande i fel meddelande och åtgärda problemet med fjärrservern.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Felkod: ArgumentOutOfRangeException

- **Meddelande**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Orsak**: i ADF stöds datetime-värden i intervallet från 0001-01-01 00:00:00 till 9999-12-31 23:59:59. Oracle stöder dock större intervall av DateTime-värde (som BC-Century eller min/SEK>59), vilket leder till att det inte går att använda ADF.

- **Rekommendation**: 

    Kör `select dump(<column name>)` för att kontrol lera om värdet i Oracle är i ADF-intervallet. 

    Om du vill veta byte-sekvensen i resultatet kontrollerar du https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle .


## <a name="orc-format"></a>Orc-format

### <a name="error-code--orcjavainvocationexception"></a>Felkod: OrcJavaInvocationException

- **Meddelande**: `An error occurred when invoking java, message: %javaException;.`

- **Orsak**: när fel meddelandet innehåller "Java. lang. OutOfMemory", "Java heap Space" och "doubleCapacity", är det vanligt vis ett minnes hanterings problem i den tidigare versionen av integration Runtime.

- **Rekommendation**: om du använder integration runtime med egen värd rekommenderar vi att du uppgraderar till den senaste versionen.

- **Orsak**: när fel meddelandet innehåller Java. lang. OutOfMemory har inte integrerings körningen tillräckligt med resurs för att bearbeta filerna.

- **Rekommendation**: begränsa samtidiga körningar i integration Runtime. För egen värd Integration Runtime skala upp till en kraftfull dator med minne som är lika med eller större än 8 GB.

- **Orsak**: när fel meddelandet innehåller "NullPointerReference" är det möjligt att ett tillfälligt fel.

- **Rekommendation**: försök igen. Kontakta supporten om problemet kvarstår.

- **Orsak**: när fel meddelandet innehåller "BufferOverflowException" är det möjligt att ett tillfälligt fel.

- **Rekommendation**: försök igen. Kontakta supporten om problemet kvarstår.

- **Orsak**: när fel meddelandet innehåller "Java. lang. ClassCastException:org. apache. Hadoop. Hive. serde2. io. HiveCharWritable kan inte omvandlas till org. apache. Hadoop. io. text", detta är typ konverterings problemet i Java Runtime. Det beror vanligt vis på att källdata inte är väl hanterade i Java Runtime.

- **Rekommendation**: Detta är data problem. Försök att använda strängen i stället för char/varchar i Orc format data.

### <a name="error-code--orcdatetimeexceedlimit"></a>Felkod: OrcDateTimeExceedLimit

- **Meddelande**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Orsak**: om datetime-värdet är "0001-01-01 00:00:00" kan det bero på skillnaden mellan Juliansk-kalendern och Gregoriansk kalender. https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates.

- **Rekommendation**: kontrol lera Ticket-värdet och Undvik att använda datetime-värdet 0001-01-01 00:00:00.


## <a name="parquet-format"></a>Parquet-format

### <a name="error-code--parquetjavainvocationexception"></a>Felkod: ParquetJavaInvocationException

- **Meddelande**: `An error occurred when invoking java, message: %javaException;.`

- **Orsak**: när fel meddelandet innehåller "Java. lang. OutOfMemory", "Java heap Space" och "doubleCapacity", är det vanligt vis ett minnes hanterings problem i den tidigare versionen av integration Runtime.

- **Rekommendation**: om du använder en egen värd integration Runtime och versionen är tidigare än 3.20.7159.1, rekommenderar vi att du uppgraderar till den senaste versionen.

- **Orsak**: när fel meddelandet innehåller Java. lang. OutOfMemory har inte integrerings körningen tillräckligt med resurs för att bearbeta filerna.

- **Rekommendation**: begränsa samtidiga körningar i integration Runtime. För egen värd Integration Runtime skala upp till en kraftfull dator med minne som är lika med eller större än 8 GB.

- **Orsak**: när fel meddelandet innehåller "NullPointerReference" är det möjligt att ett tillfälligt fel.

- **Rekommendation**: försök igen. Kontakta supporten om problemet kvarstår.


### <a name="error-code--parquetinvalidfile"></a>Felkod: ParquetInvalidFile

- **Meddelande**: `File is not a valid Parquet file.`

- **Orsak**: Parquet fil problem.

- **Rekommendation**: kontrol lera att indata är en giltig Parquet-fil.


### <a name="error-code--parquetnotsupportedtype"></a>Felkod: ParquetNotSupportedType

- **Meddelande**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Orsak**: Parquet-formatet stöds inte i Azure Data Factory.

- **Rekommendation**: dubbelt kontrol lera källdata. Referera till dokumentet: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Felkod: ParquetMissedDecimalPrecisionScale

- **Meddelande**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Orsak**: försök att parsa tal precision och skala, men det finns ingen sådan information.

- **Rekommendation**: källan returnerar inte rätt precision och skalning. Kontrol lera ärendets kolumn precision och skala.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Felkod: ParquetInvalidDecimalPrecisionScale

- **Meddelande**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Orsak**: schemat är ogiltigt.

- **Rekommendation**: kontrol lera precision och skala för ärende kolumnen.


### <a name="error-code--parquetcolumnnotfound"></a>Felkod: ParquetColumnNotFound

- **Meddelande**: `Column %column; does not exist in Parquet file.`

- **Orsak**: käll schemat matchar inte Sink-schemat.

- **Rekommendation**: kontrol lera the'mappings i Activity. Se till att käll kolumnen kan mappas till den högra kolumnen för mottagare.


### <a name="error-code--parquetinvaliddataformat"></a>Felkod: ParquetInvalidDataFormat

- **Meddelande**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Orsak**: det går inte att konvertera data till den typ som anges i mappningar. källa

- **Rekommendation**: dubbel kontrol lera källdata eller ange rätt datatyp för den här kolumnen i kolumn mappningen för kopiera aktivitet. Referera till dokumentet: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Felkod: ParquetDataCountNotMatchColumnCount

- **Meddelande**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Orsak**: det går inte att räkna ut käll kolumner och mottagar kolumn

- **Rekommendation**: dubbel kontroll av käll kolumn antal är samma som antalet för mottagar kolumn i mappningen.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Felkod: ParquetDataTypeNotMatchColumnType

- **Meddelande**: data typen% srcType; matchar inte den aktuella kolumn typen% dstType; i kolumnen% columnIndex;.

- **Orsak**: det går inte att konvertera data från källan till angivet definierade i Sink

- **Rekommendation**: Ange en korrekt typ i mappning. Sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Felkod: ParquetBridgeInvalidData

- **Meddelande**: `%message;`

- **Orsak**: data värde över begränsning

- **Rekommendation**: försök igen. Om problemet kvarstår kan du kontakta oss.


### <a name="error-code--parquetunsupportedinterpretation"></a>Felkod: ParquetUnsupportedInterpretation

- **Meddelande**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Orsak**: scenario som inte stöds

- **Rekommendation**: ' ParquetInterpretFor ' får inte vara ' sparkSql '.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Felkod: ParquetUnsupportFileLevelCompressionOption

- **Meddelande**: `File level compression is not supported for Parquet.`

- **Orsak**: scenario som inte stöds

- **Rekommendation**: ta bort CompressionType i nytto lasten.


### <a name="error-code--usererrorjniexception"></a>Felkod: UserErrorJniException

- **Meddelande**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Orsak**: det går inte att skapa JVM eftersom vissa ogiltiga (globala) argument har angetts.

- **Rekommendation**: Logga in på datorn som är värd för **varje nod** i din egen värd-IR. Kontrol lera att systemvariabeln är korrekt inställd så här: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Starta om alla IR-noder och kör sedan pipelinen igen.


### <a name="arithmetic-overflow"></a>Aritmetiskt spill

- **Symptom**: fel meddelande inträffade vid kopiering av Parquet-filer: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Orsak**: för närvarande är bara decimaler av precision <= 38 och längden på heltals delen <= 20 som stöds när du kopierar filer från Oracle till Parquet. 

- **Lösning**: du kan konvertera kolumner med det här problemet till varchar2 som en lösning.


### <a name="no-enum-constant"></a>Ingen Enum-konstant

- **Symptom**: fel meddelande inträffade vid kopiering av data till Parquet-format: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , eller: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Orsak**: 

    Problemet kan bero på blank steg eller tecken som inte stöds (t. ex.,; {} () \n\t =) i kolumn namn, eftersom Parquet inte stöder sådant format. 

    Kolumn namn som *contoso (test)* kommer till exempel att parsa typen inom hakparenteser från [kod](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . Felet aktive ras eftersom det inte finns någon sådan "test"-typ.

    Om du vill kontrol lera vilka typer som stöds kan du kontrol lera dem [här](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Lösning**: 

    - Dubbel kontroll om det finns blank steg i namn på mottagar kolumn.

    - Dubbel kontroll om den första raden med vita blank steg används som kolumn namn.

    - Dubbel kontroll av typen OriginalType stöds eller inte. Försök att undvika dessa särskilda symboler `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code--restsinkcallfailed"></a>Felkod: RestSinkCallFailed

- **Meddelande**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Orsak**: det här felet uppstår när Azure Data Factory prata med REST-slutpunkt över http-protokollet och begäran om att utföra åtgärden misslyckades.

- **Rekommendation**: kontrol lera HTTP-statuskoden \ meddelande i fel meddelande och åtgärda problemet med fjärrservern.

### <a name="unexpected-network-response-from-rest-connector"></a>Oväntat nätverks svar från REST Connector

- **Symptom**: slut punkten tar ibland emot oväntat svar (400/401/403/500) från rest Connector.

- **Orsak**: rest source Connector använder URL och http-metod/sidhuvud/brödtext från länkad tjänst/data uppsättning/kopierings källa som parametrar vid konstruktion av en http-begäran. Problemet orsakades troligen av några misstag i en eller flera angivna parametrar.

- **Lösning**: 
    - Använd "sväng" i cmd-fönstret för att kontrol lera om parametern är orsaken eller inte (**Accept** -och **User-Agent** -huvuden ska alltid ingå):
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      Om kommandot returnerar samma oväntade svar måste du åtgärda parametrarna med "sväng" tills det returnerar det förväntade svaret. 

      Du kan också använda "sväng--Help" för mer avancerad användning av kommandot.

    - Om endast ADF REST Connector returnerar oväntade svar kan du kontakta Microsoft Support för ytterligare fel sökning.
    
    - Observera att "sväng" kanske inte lämpar sig för att återskapa verifierings problemet för SSL-certifikat. I vissa fall kördes "klammer"-kommandot utan att några validerings problem med SSL-certifikatet har påträffats. Men när samma URL körs i webbläsaren, returneras inget SSL-certifikat i den första platsen där klienten kan upprätta förtroende med servern.

      Verktyg som **Postman** och **Fiddler** rekommenderas för ovanstående fall.


## <a name="sftp"></a>SFTP

#### <a name="error-code--sftpoperationfail"></a>Felkod: SftpOperationFail

- **Meddelande**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Orsak**: problem med SFTP-åtgärden.

- **Rekommendation**: kontrol lera det detaljerade felet från SFTP.


### <a name="error-code--sftprenameoperationfail"></a>Felkod: SftpRenameOperationFail

- **Meddelande**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, please set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Orsak**: din SFTP-server har inte stöd för namnbyte av Temp-filen.

- **Rekommendation**: Ange "useTempFileRename" som falskt i kopierings mottagare för att inaktivera överföring till en temporär fil.


### <a name="error-code--sftpinvalidsftpcredential"></a>Felkod: SftpInvalidSftpCredential

- **Meddelande**: `Invalid Sftp credential provided for '%type;' authentication type.`

- **Orsak**: innehåll från privat nyckel hämtas från AKV/SDK, men kodas inte korrekt.

- **Rekommendation**:  

    Om privat nyckel innehåll kommer från AKV och den ursprungliga nyckel filen kan fungera om kunden laddar upp den direkt till SFTP-länkad tjänst

    Se https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication PrivateKey-innehållet är en Base64-kodad SSH-nyckel innehåll.

    Koda **hela innehållet i den ursprungliga privata nyckel filen** med base64-kodning och lagra den kodade STRÄNGEN i akv. Den ursprungliga privata nyckel filen är den som kan fungera på en länkad SFTP-tjänst om du klickar på överför från fil.

    Här är några exempel som används för att generera strängen:

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

    - Använd base64-konverterings verktyg från tredje part

        Verktyg som https://www.base64encode.org/ rekommenderas.

- **Orsak**: felaktigt nyckel innehålls format har valts

- **Rekommendation**:  

    PKCS # 8-format privat SSH-nyckel (börja med "-----starta KRYPTERAd privat nyckel-----") stöds för närvarande inte för att komma åt SFTP-servern i ADF. 

    Kör följande kommandon för att konvertera nyckeln till traditionellt SSH-nyckelpar (börja med "-----påbörja privat RSA-nyckel-----"):

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Orsak**: ogiltig autentiseringsuppgift eller privat nyckel innehåll

- **Rekommendation**: dubbel kontroll med verktyg som WinSCP för att se om nyckel filen eller lösen ordet är korrekt.

### <a name="sftp-copy-activity-failed"></a>Det gick inte att kopiera SFTP

- **Symptom**: felkod: UserErrorInvalidColumnMappingColumnNotFound. Fel meddelande: `Column &apos;AccMngr&apos; specified in column mapping cannot be found in source data.`

- **Orsak**: källan innehåller inte en kolumn med namnet "AccMngr".

- **Lösning**: dubbelt kontrol lera hur din data uppsättning konfigureras genom att mappa kolumnen mål data mängd för att bekräfta om det finns en sådan "AccMngr"-kolumn.


### <a name="error-code--sftpfailedtoconnecttosftpserver"></a>Felkod: SftpFailedToConnectToSftpServer

- **Meddelande**: `Failed to connect to Sftp server '%server;'.`

- **Orsak**: om fel meddelandet innehåller tids gränsen nåddes för socket-läsning efter 30000 millisekunder, är en möjlig orsak att felaktig länkad tjänst typ används för SFTP-servern, t. ex. att använda kopplad FTP-tjänst för att ansluta till en SFTP-server.

- **Rekommendation**: kontrol lera mål serverns port. Som standard använder SFTP port 22.

- **Orsak**: om fel meddelandet innehåller "Server svaret innehåller inte SSH-protokoll-ID", kan en möjlig orsak vara att SFTP-servern begränsar anslutningen. ADF skapar flera anslutningar för att ladda ned från SFTP-servern parallellt, och ibland kommer den att nå en begränsning i SFTP-servern. Praktiskt taget returnerar en annan server ett annat fel när du träffar på begränsningen.

- **Rekommendation**:  

    Ange max för samtidig anslutning av SFTP-datauppsättning till 1 och kör kopieringen igen. Om det lyckas kan vi vara säker på att begränsningen är orsaken.

    Kontakta SFTP-administratören om du vill höja gränsen för antalet samtidiga anslutningar, eller Lägg till under IP för att tillåta listan:

    - Om du använder hanterad IR måste du lägga till [IP-intervall för Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653).
      Du kan också installera IR med egen värd om du inte vill lägga till en stor lista med IP-intervall i listan över tillåtna i SFTP-servern.

    - Om du använder IR med egen värd lägger du till den dator-IP som installerade SHIR i listan över tillåtna.


## <a name="sharepoint-online-list"></a>SharePoint Online-lista

### <a name="error-code--sharepointonlineauthfailed"></a>Felkod: SharePointOnlineAuthFailed

- **Meddelande**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Orsak**: tjänstens huvud NAMNS-ID och nyckel kanske inte har angetts korrekt.

- **Rekommendation**: kontrol lera ditt registrerade program (tjänstens huvud NAMNS-ID) och ange om det har angetts korrekt.


## <a name="xml-format"></a>XML-format

### <a name="error-code--xmlsinknotsupported"></a>Felkod: XmlSinkNotSupported

- **Meddelande**: `Write data in xml format is not supported yet, please choose a different format!`

- **Orsak**: Använd en XML-datauppsättning som mottagar data uppsättning i din kopierings aktivitet

- **Rekommendation**: Använd en data uppsättning i ett annat format som kopierings mottagare.


### <a name="error-code--xmlattributecolumnnameconflict"></a>Felkod: XmlAttributeColumnNameConflict

- **Meddelande**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Orsak**: ett attributvärde som orsakade konflikten användes.

- **Rekommendation**: Ange ett annat värde för egenskapen "attributePrefix".


### <a name="error-code--xmlvaluecolumnnameconflict"></a>Felkod: XmlValueColumnNameConflict

- **Meddelande**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Orsak**: ett av de underordnade element namnen används som kolumn namn för elementets värde.

- **Rekommendation**: Ange ett annat värde för egenskapen "valueColumn".


### <a name="error-code--xmlinvalid"></a>Felkod: XmlInvalid

- **Meddelande**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Orsak**: indata-XML-filen är inte korrekt formaterad.

- **Rekommendation**: korrigera XML-filen så att den blir korrekt formaterad.


## <a name="general-copy-activity-error"></a>Fel vid allmän kopierings aktivitet

### <a name="error-code--jrenotfound"></a>Felkod: JreNotFound

- **Meddelande**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Orsak**: den egna värdbaserade integrerings körningen kan inte hitta Java Runtime. Java Runtime krävs för att läsa en viss källa.

- **Rekommendation**: kontrol lera integrerings körnings miljön, referens dokumentet: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Felkod: WildcardPathSinkNotSupported

- **Meddelande**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Orsak**: Sink-datamängden stöder inte jokertecken.

- **Rekommendation**: kontrol lera Sink-datauppsättningen och åtgärda sökvägen utan jokertecken.


### <a name="fips-issue"></a>FIPS-problem

- **Symptom**: kopierings aktiviteten Miss lyckas på den FIPS-aktiverade integration runtime datorn med fel meddelande `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` . Detta kan inträffa när du kopierar data med kopplingar som Azure Blob, SFTP osv.

- **Orsak**: FIPS (Federal Information Processing Standards) definierar en viss uppsättning kryptografiska algoritmer som får användas. När FIPS-läge är aktiverat på datorn blockeras vissa kryptografiska klasser som kopierar aktivitet i vissa scenarier.

- **Lösning**: du kan lära dig om den aktuella situationen i FIPS-läge i Windows från [den här artikeln](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)och utvärdera om du kan inaktivera fips på den lokala integration runtime datorn.

    Om du däremot bara vill låta Azure Data Factory kringgå FIPS och göra så att aktiviteten körs korrekt, kan du följa dessa steg:

    1. Öppna mappen där den egna Integration Runtime är installerad, vanligt vis under `C:\Program Files\Microsoft Integration Runtime\<IR version>\Shared` .

    2. Öppna "diawp.exe.config" och Lägg till `<enforceFIPSPolicy enabled="false"/>` i `<runtime>` avsnittet som nedan.

        ![Inaktivera FIPS](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Starta om datorn med egen värd Integration Runtime.


## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Sidan Microsoft Q&en fråga](/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
