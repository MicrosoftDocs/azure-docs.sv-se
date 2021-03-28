---
title: Felsöka data flöden för mappning
description: Lär dig hur du felsöker data flödes problem i Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.openlocfilehash: 7678d0fde21cefc950e0ac64a58563425c606298
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640217"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Felsöka mappning av data flöden i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln utforskar vanliga fel söknings metoder för att mappa data flöden i Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Vanliga felkoder och meddelanden 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Felkod: DF-utförar-SourceInvalidPayload
- **Meddelande**: det gick inte att köra data flöde för förhands granskning, fel sökning och pipeline-data flöde eftersom behållaren inte finns
- **Orsak**: en data uppsättning innehåller en behållare som inte finns i lagrings utrymmet.
- **Rekommendation**: kontrol lera att behållaren som refereras till i data uppsättningen finns och att den kan nås.

### <a name="error-code-df-executor-systeminvalidjson"></a>Felkod: DF-utförar-SystemInvalidJson

- **Meddelande**: JSON-parsningsfel, kodning eller Multiline stöds inte
- **Orsak**: möjliga problem med JSON-filen: kodning som inte stöds, skadade byte eller med JSON-källa som ett enda dokument på flera kapslade rader.
- **Rekommendation**: kontrol lera att JSON-filens kodning stöds. I käll omvandlingen som använder en JSON-datauppsättning expanderar du **JSON-inställningar** och aktiverar **enstaka dokument**.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Felkod: DF-utförar-BroadcastTimeout

- **Meddelande**: timeout-fel vid sändnings anslutning, kontrol lera att sändnings strömmen genererar data inom 60 sekunder i fel söknings körningar och 300 sekunder i jobb körningar
- **Orsak**: sändningen har en standard tids gräns på 60 sekunder för fel söknings körningar och 300 sekunder för jobb körningar. Den data ström som valts för sändning är för stor för att producera data inom den här gränsen.
- **Rekommendation**: kontrol lera att fliken **optimera** finns i dina data flödes omvandlingar för Join, exists och lookup. Standard alternativet för sändning är **Auto**. Om **Auto** har angetts, eller om du manuellt ställer in den vänstra eller högra sidan för sändning under **fast**, kan du antingen ange en större IR-konfiguration (Azure integration Runtime) eller inaktivera sändning. För bästa prestanda i data flöden rekommenderar vi att du tillåter Spark att sända genom att använda **Auto** och använda ett minnesoptimerade Azure IR. 
 
  Om du kör data flödet i ett fel söknings test för att köra en fel söknings-pipeline kan du köra det här villkoret oftare. Det beror på att Azure Data Factory begränsar sändningens tids gräns till 60 sekunder för att få en snabbare fel sökning. Du kan förlänga tids gränsen till 300-sekunders tids gränsen för en utlöst körning. Om du vill göra det kan du använda det  >  Azure IR som definierats i pipeline-aktiviteten kör data flöde för **att använda** alternativet för att felsöka.

- **Meddelande**: timeout-fel i sändnings anslutning. du kan välja "off" av sändnings alternativet i sammanfogning/exists/lookup-transformeringen för att undvika det här problemet. Om du vill skicka kopplings alternativ för att förbättra prestandan ska du kontrol lera att sändnings strömmen kan producera data inom 60 sekunder i fel söknings körningar och 300 sekunder i jobb körningarna.
- **Orsak**: sändningen har en standard-timeout på 60 sekunder i fel söknings körningar och 300 sekunder i jobbet körs. På sändnings anslutningen är den data ström som valts för sändning är för stor för att producera data inom den här gränsen. Om en sändnings anslutning inte används, kan standard sändningen via data ström uppnå samma gräns.
- **Rekommendation**: inaktivera sändnings alternativet eller Undvik att sända stora data strömmar för vilka bearbetningen kan ta mer än 60 sekunder. Välj en mindre ström att sända. Stora Azure SQL Data Warehouse tabeller och källfiler är vanligt vis lämpliga alternativ. Om det inte finns någon sändnings anslutning använder du ett större kluster om felet uppstår.

### <a name="error-code-df-executor-conversion"></a>Felkod: DF-utförar-Conversion

- **Meddelande**: det gick inte att konvertera till ett datum eller en tid på grund av ett ogiltigt Character
- **Orsak**: data har inte det förväntade formatet.
- **Rekommendation**: Använd rätt datatyp.

### <a name="error-code-df-executor-invalidcolumn"></a>Felkod: DF-utförar-InvalidColumn
- **Meddelande**: kolumn namnet måste anges i frågan, ange ett alias om du använder en SQL-funktion
- **Orsak**: inget kolumn namn har angetts.
- **Rekommendation**: Ange ett alias om du använder en SQL-funktion som min () eller Max ().

### <a name="error-code-df-executor-drivererror"></a>Felkod: DF-utförar-DriverError
- **Meddelande**: INT96 är en äldre timestamp-typ som inte stöds av ADF-dataflöde. Överväg att uppgradera kolumn typen till de senaste typerna.
- **Orsak**: driv rutins fel.
- **Rekommendation**: INT96 är en äldre timestamp-typ som inte stöds av Azure Data Factory data flöde. Överväg att uppgradera kolumn typen till den senaste typen.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Felkod: DF-utförar-BlockCountExceedsLimitError
- **Meddelande**: antalet icke-allokerade block får inte överskrida Max gränsen på 100 000 block. Kontrol lera BLOB-konfigurationen.
- **Orsak**: det maximala antalet icke allokerade block i en blob är 100 000.
- **Rekommendation**: kontakta Microsofts produkt team om du vill ha mer information om det här problemet.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Felkod: DF-utförar-PartitionDirectoryError
- **Meddelande**: den angivna käll Sök vägen har antingen flera partitionerade kataloger (t. ex. <Source Path> /<partitionens rot Katalog 1>/a = 10/b = 20, <Source Path> /<partitionens rot Katalog 2>/c = 10/d = 30) eller partitionerad katalog med andra fil-eller icke-partitionerade kataloger (till exempel <Source Path> /<partitions rot Katalog 1>/a = 10/b = 20, <Source Path> /katalog 2/fil1), ta bort partitionens rot Katalog från käll Sök vägen och Läs den via separat käll omvandling.
- **Orsak**: käll Sök vägen har antingen flera partitionerade kataloger eller en partitionerad katalog som har en annan fil eller en icke-partitionerad katalog.
- **Rekommendation**: ta bort den partitionerade rot katalogen från käll Sök vägen och Läs den via separat käll omvandling.

### <a name="error-code-df-executor-invalidtype"></a>Felkod: DF-utförar-InvalidType
- **Meddelande**: kontrol lera att den typ av parameter som matchar typen av värde har överförts. Det finns för närvarande inte stöd för att skicka flytt ALS parametrar från pipeliner.
- **Orsak**: data typen för den deklarerade typen är inte kompatibel med det faktiska parametervärdet.
- **Rekommendation**: kontrol lera att de parameter värden som skickas till data flödet matchar den deklarerade typen.

### <a name="error-code-df-executor-parseerror"></a>Felkod: DF-utförar-ParseError
- **Meddelande**: det går inte att parsa uttrycket
- **Orsak**: ett uttryck genererade tolknings fel på grund av felaktig formatering.
- **Rekommendation**: kontrol lera formateringen i uttrycket.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Felkod: DF-utförar-SystemImplicitCartesian
- **Meddelande**: implicit kartesiska-produkt för inre koppling stöds inte, Använd kors koppling i stället. Kolumner som används i Join ska skapa en unik nyckel för rader.
- **Orsak**: implicita kartesiska-produkter för inre kopplingar mellan logiska planer stöds inte. Om du använder kolumner i kopplingen skapar du en unik nyckel.
- **Rekommendation**: Använd kors koppling för icke-jämlikhetbaserade kopplingar.

### <a name="error-code-getcommand-outputasync-failed"></a>Felkod: GetCommand OutputAsync misslyckades
- **Meddelande**: vid fel sökning av data flöde och data förhands granskning: GetCommand OutputAsync misslyckades med...
- **Orsak**: det här felet är ett Server dels fel. 
- **Rekommendation**: försök igen och starta om felsökningssessionen. Kontakta kund support om du inte kan lösa problemet genom att försöka igen och starta om. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Felkod: DF-utförar-OutOfMemoryError
 
- **Meddelande**: klustret fick ett slut på minnes problem under körningen, försök igen med en integrerings körning med större antal kärnor och/eller minnesoptimerade beräknings typ
- **Orsak**: klustret har slut på minne.
- **Rekommendation**: fel söknings kluster är avsedda för utveckling. Använd data sampling och en lämplig beräknings typ och storlek för att köra nytto lasten. Prestanda tips finns i [prestanda guiden för att mappa data flöde](concepts-data-flow-performance.md).

### <a name="error-code-df-executor-illegalargument"></a>Felkod: DF-utförar-illegalArgument

- **Meddelande**: kontrol lera att åtkomst nyckeln i den länkade tjänsten är korrekt
- **Orsak**: konto namnet eller åtkomst nyckeln är felaktig.
- **Rekommendation**: kontrol lera att konto namnet eller åtkomst nyckeln som anges i den länkade tjänsten är korrekt. 

### <a name="error-code-df-executor-columnunavailable"></a>Felkod: DF-utförar-ColumnUnavailable
- **Meddelande**: kolumn namnet som används i uttrycket är inte tillgängligt eller ogiltigt.
- **Orsak**: ett ogiltigt eller otillgängligt kolumn namn används i ett uttryck.
- **Rekommendation**: kontrol lera kolumn namnen som används i uttryck.

 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Felkod: DF-utförar-OutOfDiskSpaceError
- **Meddelande**: internt Server fel
- **Orsak**: klustrets disk utrymme håller på att ta slut.
- **Rekommendation**: försök att köra pipelinen igen. Om detta inte löser problemet kontaktar du kund support.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Felkod: DF-utförar-StoreIsNotDefined
- **Meddelande**: Store-konfigurationen har inte definierats. Det här felet kan bero på ogiltig parameter tilldelning i pipelinen.
- **Orsak**: obestämd.
- **Rekommendation**: kontrol lera tilldelning av parameter värde i pipelinen. Ett parameter uttryck kan innehålla ogiltiga tecken.


### <a name="error-code-4502"></a>Felkod: 4502
- **Meddelande**: det finns betydande samtidiga MappingDataflow-körningar som orsakar fel på grund av begränsning av integration Runtime.
- **Orsak**: ett stort antal körningar av data flödes aktiviteter sker samtidigt i integration Runtime. Mer information finns i [Azure Data Factory gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Rekommendation**: om du vill köra fler data flödes aktiviteter parallellt distribuerar du dem över flera integrerings körningar.


### <a name="error-code-invalidtemplate"></a>Felkod: InvalidTemplate
- **Meddelande**: det går inte att utvärdera pipeline-uttrycket.
- **Orsak**: det pipeline-uttryck som skickades i data flödes aktiviteten bearbetas inte korrekt på grund av ett syntaxfel.
- **Rekommendation**: kontrol lera aktiviteten i aktivitets övervakning för att verifiera uttrycket.

### <a name="error-code-2011"></a>Felkod: 2011
- **Meddelande**: aktiviteten kördes på Azure integration Runtime och det gick inte att dekryptera autentiseringsuppgifterna för data lagret eller data bearbetningen som är ansluten via en egen värd integration Runtime. Kontrol lera konfigurationen av länkade tjänster som är associerade med den här aktiviteten och se till att du använder rätt typ av integration Runtime.
- **Orsak**: data flödet stöder inte länkade tjänster på integration runtime med egen värd.
- **Rekommendation**: Konfigurera data flöde som ska köras på en hanterad Virtual Network integration Runtime.

### <a name="error-code-df-xml-invalidvalidationmode"></a>Felkod: DF-XML-InvalidValidationMode
- **Meddelande**: ogiltigt läge för XML-validering har angetts.
- **Rekommendation**: kontrol lera värdet för parametern och ange rätt validerings läge.

### <a name="error-code-df-xml-invaliddatafield"></a>Felkod: DF-XML-InvalidDataField
- **Meddelande**: fältet för skadade poster måste vara av sträng typ och kan ha värdet null.
- **Rekommendation**: kontrol lera att kolumnen `\"_corrupt_record\"` i käll projektet har en sträng data typ.

### <a name="error-code-df-xml-malformedfile"></a>Felkod: DF-XML-MalformedFile
- **Meddelande**: felaktig XML-kod i FailFastMode.
- **Rekommendation**: uppdatera innehållet i XML-filen till rätt format.

### <a name="error-code-df-xml-invaliddatatype"></a>Felkod: DF-XML-InvalidDataType
- **Meddelande**: XML-element innehåller under element eller attribut och kan inte konverteras.

### <a name="error-code-df-xml-invalidreferenceresource"></a>Felkod: DF-XML-InvalidReferenceResource
- **Meddelande**: det går inte att matcha referens resursen i XML-datafilen.
- **Rekommendation**: du bör kontrol lera referens resursen i XML-datafilen.

### <a name="error-code-df-xml-invalidschema"></a>Felkod: DF-XML-InvalidSchema
- **Meddelande**: schema valideringen misslyckades.

### <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>Felkod: DF-XML-UnsupportedExternalReferenceResource
- **Meddelande**: det finns inte stöd för extern referens resurs i XML-datafilen.
- **Rekommendation**: uppdatera XML-filens innehåll när den externa referens resursen inte stöds nu.

### <a name="error-code-df-gen2-invalidaccountconfiguration"></a>Felkod: DF-GEN2-InvalidAccountConfiguration
- **Meddelande**: antingen en av konto nyckeln eller Tenant/SpnId/SpnCredential/SpnCredentialType eller MiServiceUri/miServiceToken måste anges.
- **Rekommendation**: Konfigurera rätt konto i den relaterade länkade Gen2-tjänsten.

### <a name="error-code-df-gen2-invalidauthconfiguration"></a>Felkod: DF-GEN2-InvalidAuthConfiguration
- **Meddelande**: det går bara att ange en av de tre autentiseringsmetoderna (Key, SERVICEPRINCIPAL och mi). 
- **Rekommendation**: Välj rätt autentiseringstyp i den relaterade länkade Gen2-tjänsten.

### <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>Felkod: DF-GEN2-InvalidServicePrincipalCredentialType
- **Meddelande**: ServicePrincipalCredentialType är ogiltigt.

### <a name="error-code-df-gen2-invaliddatatype"></a>Felkod: DF-GEN2-InvalidDataType
- **Meddelande**: moln typen är ogiltig.

### <a name="error-code-df-blob-invalidaccountconfiguration"></a>Felkod: DF-BLOB-InvalidAccountConfiguration
- **Meddelande**: du måste ange antingen en av konto nyckeln eller sas_token.

### <a name="error-code-df-blob-invalidauthconfiguration"></a>Felkod: DF-BLOB-InvalidAuthConfiguration
- **Meddelande**: det går bara att ange en av de två autentiseringsmetoderna (nyckel, SAS).

### <a name="error-code-df-blob-invaliddatatype"></a>Felkod: DF-BLOB-InvalidDataType
- **Meddelande**: moln typen är ogiltig.

### <a name="error-code-df-cosmos-partitionkeymissed"></a>Felkod: DF-Cosmos-PartitionKeyMissed
- **Meddelande**: sökväg till partitionsnyckel måste anges för uppdaterings-och borttagnings åtgärder.
- **Rekommendation**: Använd den tillhandahållna partitionsnyckel i Cosmos Sink-inställningar.

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Felkod: DF-Cosmos-InvalidPartitionKey
- **Meddelande**: sökvägen till partitionsnyckel får inte vara tom för uppdaterings-och borttagnings åtgärder.
- **Rekommendation**: Använd den tillhandahållna partitionsnyckel i Cosmos Sink-inställningar.

### <a name="error-code-df-cosmos-idpropertymissed"></a>Felkod: DF-Cosmos-IdPropertyMissed
- **Meddelande**: egenskapen ID ska vara mappad för borttagnings-och uppdaterings åtgärder.
- **Rekommendation**: kontrol lera att indata innehåller en `id` kolumn i Cosmos Sink-inställningar. Om nej, Använd **Select eller härledd Transformation** för att generera den här kolumnen före Sink.

### <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>Felkod: DF-Cosmos-InvalidPartitionKeyContent
- **Meddelande**: partitionsnyckel ska börja med/.
- **Rekommendation**: gör en partitionsnyckel inleds med `/` i Cosmos Sink-inställningar, till exempel: `/movieId` .

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Felkod: DF-Cosmos-InvalidPartitionKey
- **Meddelande**: partitionKey har inte mappats i mottagare för borttagnings-och uppdaterings åtgärder.
- **Rekommendation**: Använd den partitionsnyckel som är samma som behållarens partitionsnyckel i Cosmos Sink-inställningar.

### <a name="error-code-df-cosmos-invalidconnectionmode"></a>Felkod: DF-Cosmos-InvalidConnectionMode
- **Meddelande**: ogiltigt connectionMode.
- **Rekommendation**: kontrol lera att det läge som stöds är **Gateway** och **DirectHttps** i Cosmos-inställningar.

### <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>Felkod: DF-Cosmos-InvalidAccountConfiguration
- **Meddelande**: antingen AccountName eller accountEndpoint ska anges.

### <a name="error-code-df-github-writenotsupported"></a>Felkod: DF-GitHub-WriteNotSupported
- **Meddelande**: GitHub Store tillåter inte skrivningar.

### <a name="error-code-df-pgsql-invalidcredential"></a>Felkod: DF-PGSQL-InvalidCredential
- **Meddelande**: användaren/lösen ordet ska anges.
- **Rekommendation**: kontrol lera att du har rätt inställningar för autentiseringsuppgifter i den relaterade länkade postgresql-tjänsten.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Felkod: DF-snö-InvalidStageConfiguration
- **Meddelande**: det går bara att använda Blob storage-typ som fas i Läs-/skriv åtgärd för snö.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Felkod: DF-snö-InvalidStageConfiguration
- **Meddelande**: egenskaper för snö Stage-fas ska anges med Azure Blob + SAS-autentisering.

### <a name="error-code-df-snowflake-invaliddatatype"></a>Felkod: DF-snö-InvalidDataType
- **Meddelande**: Spark-typen stöds inte i snö flingor.
- **Rekommendation**: Använd den **härledda omvandlingen** för att ändra den relaterade kolumnen i indata till sträng typen innan du kan använda snö handfat. 

### <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>Felkod: DF-Hive-InvalidBlobStagingConfiguration
- **Meddelande**: egenskaper för mellanlagring av blob-lagring måste anges.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Felkod: DF-Hive-InvalidGen2StagingConfiguration
- **Meddelande**: ADLS Gen2 lagrings-mellanlagring stöder endast autentiseringsuppgifter för tjänstens huvud namn.
- **Rekommendation**: bekräfta att du tillämpar autentiseringsuppgifterna för tjänstens huvud namn i den ADLS Gen2 länkade tjänsten som används som mellanlagring.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Felkod: DF-Hive-InvalidGen2StagingConfiguration
- **Meddelande**: ADLS Gen2 lagrings egenskaper för lagring ska anges. Antingen en av nycklarna Key eller Tenant/spnId/spnKey eller miServiceUri/miServiceToken krävs.
- **Rekommendation**: Använd rätt autentiseringsuppgifter som används som mellanlagring i registrerings data filen i den relaterade ADLS Gen2 länkade tjänsten. 

### <a name="error-code-df-hive-invaliddatatype"></a>Felkod: DF-Hive-InvalidDataType
- **Meddelande**: kolumn (er) som inte stöds.
- **Rekommendation**: uppdatera kolumnen med indata så att den matchar den datatyp som stöds av Hive.

### <a name="error-code-df-hive-invalidstoragetype"></a>Felkod: DF-Hive-InvalidStorageType
- **Meddelande**: lagrings typen kan antingen vara BLOB eller Gen2.

### <a name="error-code-df-delimited-invalidconfiguration"></a>Felkod: DF-delimited-InvalidConfiguration
- **Meddelande**: du måste ange antingen en tom rad eller ett anpassat sidhuvud.
- **Rekommendation**: ange tomma rader eller anpassade rubriker i CSV-inställningar.

### <a name="error-code-df-delimited-columndelimitermissed"></a>Felkod: DF-delimited-ColumnDelimiterMissed
- **Meddelande**: kolumn avgränsare krävs för att parsa.
- **Rekommendation**: kontrol lera att du har kolumn avgränsaren i CSV-inställningarna.

### <a name="error-code-df-mssql-invalidcredential"></a>Felkod: DF-MSSQL-InvalidCredential
- **Meddelande**: antingen en av alternativen User/PWD eller Tenant/SpnId/SpnKey eller MiServiceUri/miServiceToken måste anges.
- **Rekommendation**: Använd rätt autentiseringsuppgifter i den relaterade MSSQL-länkade tjänsten.

### <a name="error-code-df-mssql-invaliddatatype"></a>Felkod: DF-MSSQL-InvalidDataType
- **Meddelande**: fält som inte stöds.
- **Rekommendation**: ändra kolumnen indata så att den matchar den datatyp som stöds av MSSQL.

### <a name="error-code-df-mssql-invalidauthconfiguration"></a>Felkod: DF-MSSQL-InvalidAuthConfiguration
- **Meddelande**: det går bara att ange en av de tre autentiseringsmetoderna (Key, SERVICEPRINCIPAL och mi).
- **Rekommendation**: du kan bara ange en av de tre autentiseringsmetoderna (Key, SERVICEPRINCIPAL och mi) i den relaterade MSSQL-länkade tjänsten.

### <a name="error-code-df-mssql-invalidcloudtype"></a>Felkod: DF-MSSQL-InvalidCloudType
- **Meddelande**: moln typen är ogiltig.
- **Rekommendation**: kontrol lera din moln typ i relaterad MSSQL-länkad tjänst.

### <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>Felkod: DF-SQLDW-InvalidBlobStagingConfiguration
- **Meddelande**: egenskaper för mellanlagring av blob-lagring måste anges.

### <a name="error-code-df-sqldw-invalidstoragetype"></a>Felkod: DF-SQLDW-InvalidStorageType
- **Meddelande**: lagrings typen kan antingen vara BLOB eller Gen2.

### <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>Felkod: DF-SQLDW-InvalidGen2StagingConfiguration
- **Meddelande**: ADLS Gen2 lagrings-mellanlagring stöder endast autentiseringsuppgifter för tjänstens huvud namn.

### <a name="error-code-df-sqldw-invalidconfiguration"></a>Felkod: DF-SQLDW-InvalidConfiguration
- **Meddelande**: ADLS Gen2 lagrings egenskaper för lagring ska anges. Antingen en av nycklarna Key eller Tenant/spnId/spnCredential/spnCredentialType eller miServiceUri/miServiceToken krävs.

### <a name="error-code-df-delta-invalidconfiguration"></a>Felkod: DF-DELTA-InvalidConfiguration
- **Meddelande**: det går inte att ange timestamp och version samtidigt.

### <a name="error-code-df-delta-keycolumnmissed"></a>Felkod: DF-DELTA-KeyColumnMissed
- **Meddelande**: nyckel kolumn (er) måste anges för åtgärder som inte kan infogas.

### <a name="error-code-df-delta-invalidtableoperationsettings"></a>Felkod: DF-DELTA-InvalidTableOperationSettings
- **Meddelande**: återskapa och trunkera alternativ kan inte båda anges.

### <a name="error-code-df-excel-worksheetconfigmissed"></a>Felkod: DF-Excel-WorksheetConfigMissed
- **Meddelande**: namn eller index för Excel-blad krävs.
- **Rekommendation**: kontrol lera värdet för parametern och ange blad namnet eller indexet för att läsa Excel-data.

### <a name="error-code-df-excel-invalidworksheetconfiguration"></a>Felkod: DF-Excel-InvalidWorksheetConfiguration
- **Meddelande**: Excel-bladets namn och index kan inte finnas samtidigt.
- **Rekommendation**: kontrol lera värdet för parametern och ange blad namnet eller indexet för att läsa Excel-data.

### <a name="error-code-df-excel-invalidrange"></a>Felkod: DF-Excel-InvalidRange
- **Meddelande**: ogiltigt intervall har angetts.
- **Rekommendation**: kontrol lera parametervärdet och ange det giltiga intervallet enligt följande referens: Excel- [format i Azure Data Factory-Dataset egenskaper](./format-excel.md#dataset-properties).

### <a name="error-code-df-excel-worksheetnotexist"></a>Felkod: DF-Excel-WorksheetNotExist
- **Meddelande**: Excel-kalkylbladet finns inte.
- **Rekommendation**: kontrol lera värdet för parametern och ange det giltiga blad namnet eller indexet för att läsa Excel-data.

### <a name="error-code-df-excel-differentschemanotsupport"></a>Felkod: DF-Excel-DifferentSchemaNotSupport
- **Meddelande**: det finns inte stöd för att läsa Excel-filer med ett annat schema nu.

### <a name="error-code-df-excel-invaliddatatype"></a>Felkod: DF-Excel-InvalidDataType
- **Meddelande**: data typen stöds inte.

### <a name="error-code-df-excel-invalidfile"></a>Felkod: DF-Excel-InvalidFile
- **Meddelande**: en ogiltig Excel-fil tillhandahålls medan endast. xlsx och. xls stöds.


## <a name="miscellaneous-troubleshooting-tips"></a>Övriga fel söknings tips
- **Problem**: ett oväntat undantag inträffade och körningen misslyckades.
    - **Meddelande**: vid körning av data flödes aktivitet: det gick inte att köra ett oväntat undantag och körnings fel.
    - **Orsak**: det här felet är ett Server dels fel. Försök igen och starta om felsökningssessionen.
    - **Rekommendation**: om du försöker igen och omstarten inte löser problemet kontaktar du kund support.

-  **Problem**: inga utdata-data vid koppling under för hands versionen av fel söknings data.
    - **Meddelande**: det finns ett stort antal null-värden eller saknade värden som kan bero på att det inte finns några rader som samplats. Försök att uppdatera gränsen för fel söknings rad och uppdatera data.
    - **Orsak**: kopplings villkoret matchade inte några rader eller resulterade i ett stort antal null-värden under förhands granskningen av data.
    - **Rekommendation**: öka antalet rader i käll rads gränsen i **fel söknings inställningar**. Se till att välja en Azure IR som har ett data flödes kluster som är tillräckligt stort för att hantera mer data.
    
- **Problem**: validerings fel vid källa med CSV-filer med flera rader. 
    - **Meddelande**: något av följande fel meddelanden visas:
        - Den sista kolumnen är null eller saknas.
        - Schema validering vid källan misslyckades.
        - Schema import kan inte visas korrekt i UX och den sista kolumnen har ett nytt rad avstånd i namnet.
    - **Orsak**: i mappnings data flödet fungerar inte csv-källfilerna för flera rader när \r\n används som rad avgränsare. Ibland kan extra rader vid vagn returer orsaka fel. 
    - **Rekommendation**: generera filen vid källan genom att använda \n som rad avgränsare i stället för \r\n. Eller Använd kopierings aktiviteten för att konvertera CSV-filen för att använda \n som en rad avgränsare.

## <a name="general-troubleshooting-guidance"></a>Allmän fel söknings vägledning
1. Kontrol lera statusen för dina data uppsättnings anslutningar. I varje omvandling av källa och mottagare går du till den länkade tjänsten för varje data uppsättning som du använder och testar anslutningarna.
2. Kontrol lera status för dina fil-och tabell anslutningar i data flödes designern. I fel söknings läge väljer du **Förhandsgranska data** på käll omvandlingarna för att se till att du har åtkomst till dina data.
3. Om allting ser korrekt ut i data förhands granskningen, går du till pipeline-designern och sätter ditt data flöde i en pipeline-aktivitet. Felsök pipelinen för ett slut punkt till slut punkts test.

## <a name="next-steps"></a>Nästa steg

Mer hjälp med fel sökning finns i följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
