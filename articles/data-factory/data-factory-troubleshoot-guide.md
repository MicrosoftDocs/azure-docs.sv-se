---
title: Felsöka Azure Data Factory | Microsoft Docs
description: Lär dig hur du felsöker externa kontroll aktiviteter i Azure Data Factory.
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: abnarain
ms.openlocfilehash: 101e55188b8021040e2fd6bd573c1c6330241e72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382811"
---
# <a name="troubleshoot-azure-data-factory"></a>Felsöka Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln utforskar vanliga fel söknings metoder för externa kontroll aktiviteter i Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Anslutningsprogram och kopieringsaktivitet

Information om anslutnings problem, till exempel ett problem med kopierings aktiviteten, finns i [felsöka Azure Data Factory anslutningar](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>Felkod: 3200

- **Meddelande**: fel 403.

- **Orsak**: `The Databricks access token has expired.`

- **Rekommendation**: som standard är Azure Databricks åtkomsttoken giltig i 90 dagar. Skapa en ny token och uppdatera den länkade tjänsten.

### <a name="error-code-3201"></a>Felkod: 3201

- **Meddelande**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Orsak**: `Bad authoring: Notebook path not specified correctly.`

- **Rekommendation**: Ange antecknings bokens sökväg i Databricks-aktiviteten.

<br/> 

- **Meddelande**: `Cluster... does not exist.`

- **Orsak**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Rekommendation**: kontrol lera att Databricks-klustret finns.

<br/> 

- **Meddelande**: `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Orsak**: `Bad authoring.`

- **Rekommendation**: Ange antingen absoluta sökvägar för arbets ytans adresserings scheman eller `dbfs:/folder/subfolder/foo.py` för filer som lagras i Databricks File System (DFS).

<br/> 

- **Meddelande**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Orsak**: `Bad authoring.`

- **Rekommendation**: verifiera den [länkade tjänst definitionen](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Meddelande**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Orsak**: `Bad authoring.`

- **Rekommendation**: verifiera den [länkade tjänst definitionen](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Meddelande**: `Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **Orsak**: `Bad authoring.`

- **Rekommendation**: se fel meddelandet.

<br/>

### <a name="error-code-3202"></a>Felkod: 3202

- **Meddelande**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **Orsak**: `Too many Databricks runs in an hour.`

- **Rekommendation**: kontrol lera alla pipeliner som använder den här Databricks-arbetsytan för att skapa takten för jobbet. Om pipelines startade för många Databricks-körningar i mängd, migrerar du några pipelines till en ny arbets yta.

<br/> 

- **Meddelande**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Orsak**: `Authoring error: No value provided for the parameter.`

- **Rekommendation**: kontrol lera pipeline-JSON och se till att alla parametrar i baseParameters Notebook anger ett värde som inte är tomt.

<br/> 

- **Meddelande**: `User: ` SimpleUserContext {userId =..., name = user@company.com , orgId =...}` is not authorized to access cluster.`

- **Orsak**: användaren som genererade åtkomsttoken får inte åtkomst till det Databricks-kluster som anges i den länkade tjänsten.

- **Rekommendation**: se till att användaren har de behörigheter som krävs på arbets ytan.

### <a name="error-code-3203"></a>Felkod: 3203

- **Meddelande**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Orsak**: klustret avslutades. För interaktiva kluster kan det här problemet vara ett tävlings tillstånd.

- **Rekommendation**: om du vill undvika det här felet använder du jobb kluster.

### <a name="error-code-3204"></a>Felkod: 3204

- **Meddelande**: `Job execution failed.`

- **Orsak**: fel meddelanden indikerar olika problem, till exempel ett oväntat kluster tillstånd eller en speciell aktivitet. Ofta visas inget fel meddelande.

- **Rekommendation**: ej tillämpligt

### <a name="error-code-3208"></a>Felkod: 3208

- **Meddelande**: `An error occurred while sending the request.`

- **Orsak**: nätverks anslutningen till Databricks-tjänsten avbröts.

- **Rekommendation**: om du använder en egen värd för integration runtime kontrollerar du att nätverks anslutningen är tillförlitlig från integration runtime-noderna. Om du använder Azure integration runtime fungerar återförsöket vanligt vis.
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Följande tabell gäller för U-SQL.
 
### <a name="error-code-2709"></a>Felkod: 2709

- **Meddelande**: `The access token is from the wrong tenant.`

- **Orsak**: felaktig Azure Active Directory (Azure AD)-klient.

- **Rekommendation**: felaktig Azure Active Directory (Azure AD)-klient.

<br/>

- **Meddelande**: `We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **Orsak**: det här felet orsakas av begränsning på data Lake Analytics.

- **Rekommendation**: minska antalet skickade jobb till data Lake Analytics. Ändra antingen Data Factory utlösare och concurrency-inställningar eller öka gränserna för Data Lake Analytics.

<br/> 

- **Meddelande**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Orsak**: det här felet orsakas av begränsning på data Lake Analytics.

- **Rekommendation**: minska antalet skickade jobb till data Lake Analytics. Ändra antingen Data Factory utlösare och concurrency-inställningar eller öka gränserna för Data Lake Analytics.

### <a name="error-code-2705"></a>Felkod: 2705

- **Meddelande**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Orsak**: tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet.

- **Rekommendation**: kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för data Lake Analytics jobb har åtkomst till både data Lake Analytics-kontot och standard-data Lake Storage-instansen från rotmappen.

### <a name="error-code-2711"></a>Felkod: 2711

- **Meddelande**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Orsak**: tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet.

- **Rekommendation**: kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för data Lake Analytics jobb har åtkomst till både data Lake Analytics-kontot och standard-data Lake Storage-instansen från rotmappen.

<br/> 

- **Meddelande**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Orsak**: sökvägen till U-SQL-filen är felaktig eller också har autentiseringsuppgifterna för den länkade tjänsten inte åtkomst.

- **Rekommendation**: kontrol lera sökvägen och autentiseringsuppgifterna som anges i den länkade tjänsten.

### <a name="error-code-2704"></a>Felkod: 2704

- **Meddelande**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Orsak**: tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet.

- **Rekommendation**: kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för data Lake Analytics jobb har åtkomst till både data Lake Analytics-kontot och standard-data Lake Storage-instansen från rotmappen.

### <a name="error-code-2707"></a>Felkod: 2707

- **Meddelande**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **Orsak**: det data Lake Analytics kontot i den länkade tjänsten är felaktigt.

- **Rekommendation**: kontrol lera att rätt konto har angetts.

### <a name="error-code-2703"></a>Felkod: 2703

- **Meddelande**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **Orsak**: felet är från data Lake Analytics.

- **Rekommendation**: jobbet skickades till data Lake Analytics och skriptet där misslyckades. Undersök i Data Lake Analytics. I portalen går du till Data Lake Analytics kontot och letar efter jobbet med hjälp av Data Factory aktivitetens körnings-ID (Använd inte pipelinens körnings-ID). Jobbet innehåller mer information om felet och hjälper dig att felsöka.

   Om upplösningen inte är klar kontaktar du Data Lake Analytics support och anger den URL (Universal Resource Locator) som innehåller ditt konto namn och jobb-ID: t.
 
## <a name="azure-functions"></a>Azure Functions

### <a name="error-code-3602"></a>Felkod: 3602

- **Meddelande**: `Invalid HttpMethod: '%method;'.`

- **Orsak**: den HttpMethod som anges i aktivitets nytto lasten stöds inte av Azure Function-aktiviteten.

- **Rekommendation**: de Httpmethods som stöds är: placera, posta, Hämta, ta bort, alternativ, huvud och spåra.

### <a name="error-code-3603"></a>Felkod: 3603

- **Meddelande**: `Response Content is not a valid JObject.`

- **Orsak**: Azure-funktionen som anropades returnerade inte en JSON-nyttolast i svaret. Azure Data Factory (ADF) Azure Function-aktivitet stöder endast JSON-svars innehåll.

- **Rekommendation**: Uppdatera Azure-funktionen för att returnera en giltig JSON-nyttolast, till exempel en C#-funktion kan returnera `(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>Felkod: 3606

- **Meddelande**: Azure Function-aktivitet saknar funktions nyckel.

- **Orsak**: Azure Function aktivitets definition har inte slutförts.

- **Rekommendation**: kontrol lera att JSON-definitionen för inmatade Azure Function-aktiviteter har en egenskap med namnet `functionKey` .

### <a name="error-code-3607"></a>Felkod: 3607

- **Meddelande**: `Azure function activity missing function name.`

- **Orsak**: Azure Function aktivitets definition har inte slutförts.

- **Rekommendation**: kontrol lera att JSON-definitionen för inmatade Azure Function-aktiviteter har en egenskap med namnet `functionName` .

### <a name="error-code-3608"></a>Felkod: 3608

- **Meddelande**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Orsak**: Azure Function-information i aktivitets definitionen kan vara felaktig.

- **Rekommendation**: korrigera Azure Function-information och försök igen.

### <a name="error-code-3609"></a>Felkod: 3609

- **Meddelande**: `Azure function activity missing functionAppUrl.`

- **Orsak**: Azure Function aktivitets definition har inte slutförts.

- **Rekommendation**: kontrol lera att JSON-definitionen för inmatade Azure Function-aktiviteter har en egenskap med namnet `functionAppUrl` .

### <a name="error-code-3610"></a>Felkod: 3610

- **Meddelande**: `There was an error while calling endpoint.`

- **Orsak**: funktions webb adressen kan vara felaktig.

- **Rekommendation**: kontrol lera att värdet för `functionAppUrl` i aktivitets-JSON är korrekt och försök igen.

### <a name="error-code-3611"></a>Felkod: 3611

- **Meddelande**: `Azure function activity missing Method in JSON.`

- **Orsak**: Azure Function aktivitets definition har inte slutförts.

- **Rekommendation**: kontrol lera att JSON-definitionen för inmatade Azure Function-aktiviteter har en egenskap med namnet `method` .

### <a name="error-code-3612"></a>Felkod: 3612

- **Meddelande**: `Azure function activity missing LinkedService definition in JSON.`

- **Orsak**: Azure Function aktivitets definition har inte slutförts.

- **Rekommendation**: kontrol lera att JSON-definitionen för inmatade Azure Function-aktiviteter innehåller länkad tjänst information.

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>Felkod: 4101

- **Meddelande**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Orsak**: felaktigt format eller saknar egenskaps definition `%propertyName;` .

- **Rekommendation**: kontrol lera om aktiviteten `%activityName;` har den egenskap `%propertyName;` som definierats med rätt data.

### <a name="error-code-4110"></a>Felkod: 4110

- **Meddelande**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Orsak**: aktivitets definitionen för AzureMLExecutePipeline har inte slutförts.

- **Rekommendation**: kontrol lera att JSON-definitionen för indata-AzureMLExecutePipeline aktivitet har korrekt länkad tjänst information.

### <a name="error-code-4111"></a>Felkod: 4111

- **Meddelande**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Orsak**: felaktig aktivitets definition.

- **Rekommendation**: kontrol lera att JSON-definitionen för indata-AzureMLExecutePipeline aktivitet har korrekt länkad tjänst information.

### <a name="error-code-4112"></a>Felkod: 4112

- **Meddelande**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Orsak**: felaktigt format eller en definition av egenskapen% PropertyName; saknas.

- **Rekommendation**: kontrol lera om den länkade tjänsten har den egenskap `%propertyName;` som definierats med rätt data.

### <a name="error-code-4121"></a>Felkod: 4121

- **Meddelande**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: autentiseringsuppgifterna som används för att komma åt Azure Machine Learning har upphört att gälla.

- **Rekommendation**: kontrol lera att autentiseringsuppgiften är giltig och försök igen.

### <a name="error-code-4122"></a>Felkod: 4122

- **Meddelande**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: autentiseringsuppgifterna som angavs i Azure Machine Learning länkade tjänsten är ogiltiga eller har inte behörighet för åtgärden.

- **Rekommendation**: kontrol lera att autentiseringsuppgiften i den länkade tjänsten är giltig och har behörighet att komma åt Azure Machine Learning.

### <a name="error-code-4123"></a>Felkod: 4123

- **Meddelande**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: egenskaperna för aktiviteten, som `pipelineParameters` är ogiltiga för pipelinen Azure Machine Learning (ml).

- **Rekommendation**: kontrol lera att värdet för aktivitets egenskaperna matchar den förväntade nytto lasten för den publicerade Azure ml-pipeline som anges i den länkade tjänsten.

### <a name="error-code-4124"></a>Felkod: 4124

- **Meddelande**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: slut punkten för den publicerade Azure ml-pipelinen finns inte.

- **Rekommendation**: kontrol lera att den publicerade Azure Machine Learning pipelinen som angavs i den länkade tjänsten finns i Azure Machine Learning.

### <a name="error-code-4125"></a>Felkod: 4125

- **Meddelande**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: ett Server fel uppstod på Azure Machine Learning.

- **Rekommendation**: försök igen senare. Kontakta Azure Machine Learnings teamet för hjälp om problemet kvarstår.

### <a name="error-code-4126"></a>Felkod: 4126

- **Meddelande**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Orsak**: körningen av Azure ml-pipeline misslyckades.

- **Rekommendation**: kontrol lera Azure Machine Learning för fler fel loggar och korrigera sedan ml-pipelinen.

## <a name="common"></a>Common

### <a name="error-code-2103"></a>Felkod: 2103

- **Meddelande**: `Please provide value for the required property '%propertyName;'.`

- **Orsak**: det obligatoriska värdet för egenskapen har inte angetts.

- **Rekommendation**: Ange värdet från meddelandet och försök igen.

### <a name="error-code-2104"></a>Felkod: 2104

- **Meddelande**: `The type of the property '%propertyName;' is incorrect.`

- **Orsak**: den angivna egenskaps typen är inte korrekt.

- **Rekommendation**: korrigera egenskapens typ och försök igen.

### <a name="error-code-2105"></a>Felkod: 2105

- **Meddelande**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Orsak**: egenskapens värde är ogiltigt eller har inte det förväntade formatet.

- **Rekommendation**: Läs dokumentationen för egenskapen och kontrol lera att det angivna värdet inkluderar rätt format och typ.

### <a name="error-code-2106"></a>Felkod: 2106

- **Meddelande**: `The storage connection string is invalid. %errorMessage;`

- **Orsak**: anslutnings strängen för lagrings platsen är ogiltig eller har fel format.

- **Rekommendation**: gå till Azure Portal och hitta lagringen, kopiera och klistra in anslutnings strängen i den länkade tjänsten och försök igen.

### <a name="error-code-2108"></a>Felkod: 2108

- **Meddelande**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Orsak**: begäran misslyckades på grund av ett underliggande problem, till exempel nätverks anslutning, DNS-fel, verifiering av Server certifikat eller tids gräns.

- **Rekommendation**: Använd Fiddler/Postman för att verifiera begäran.

### <a name="error-code-2110"></a>Felkod: 2110

- **Meddelande**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Orsak**: den länkade tjänsten som angetts i aktiviteten är felaktig.

- **Rekommendation**: kontrol lera att den länkade tjänst typen är en av de typer som stöds för aktiviteten. Till exempel kan den länkade tjänst typen för HDI-aktiviteter vara HDInsight eller HDInsightOnDemand.

### <a name="error-code-2111"></a>Felkod: 2111

- **Meddelande**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Orsak**: typen för den angivna egenskapen är inte korrekt.

- **Rekommendation**: korrigera egenskaps typen och försök igen.

### <a name="error-code-2112"></a>Felkod: 2112

- **Meddelande**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Orsak**: moln typen stöds inte eller kunde inte fastställas för lagring från EndpointSuffix.

- **Rekommendation**: Använd lagring i ett annat moln och försök igen.

### <a name="error-code-2128"></a>Felkod: 2128

- **Meddelande**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Orsak**: nätverks anslutning, DNS-fel, verifiering av Server certifikat eller tids gräns.

- **Rekommendation**: kontrol lera att slut punkten som du försöker trycka på svarar på begär Anden. Du kan använda verktyg som Fiddler/Postman.

## <a name="custom"></a>Anpassad

Följande tabell gäller för Azure Batch.
 
### <a name="error-code-2500"></a>Felkod: 2500

- **Meddelande**: `Hit unexpected exception and execution failed.`

- **Orsak**: `Can't launch command, or the program returned an error code.`

- **Rekommendation**: kontrol lera att den körbara filen finns. Om programmet har startats kontrollerar du att *stdout.txt* och *stderr.txt* har överförts till lagrings kontot. Det är en bra idé att inkludera loggar i koden för fel sökning.

### <a name="error-code-2501"></a>Felkod: 2501

- **Meddelande**: `Cannot access user batch account; please check batch account settings.`

- **Orsak**: felaktig nyckel för batch-åtkomst eller poolnamn.

- **Rekommendation**: verifiera poolens namn och grupp åtkomst nyckeln i den länkade tjänsten.

### <a name="error-code-2502"></a>Felkod: 2502

- **Meddelande**: `Cannot access user storage account; please check storage account settings.`

- **Orsak**: felaktigt lagrings konto namn eller åtkomst nyckel.

- **Rekommendation**: kontrol lera lagrings kontots namn och åtkomst nyckeln i den länkade tjänsten.

### <a name="error-code-2504"></a>Felkod: 2504

- **Meddelande**: `Operation returned an invalid status code 'BadRequest'.`

- **Orsak**: för många filer i den `folderPath` anpassade aktiviteten. Den totala storleken på `resourceFiles` får inte vara mer än 32 768 tecken.

- **Rekommendation**: ta bort onödiga filer eller zip-filer och Lägg till ett zippa-kommando för att extrahera dem.
   
   Använd till exempel `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>Felkod: 2505

- **Meddelande**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Orsak**: anpassade aktiviteter stöder bara lagrings konton som använder en åtkomst nyckel.

- **Rekommendation**: se fel beskrivningen.

### <a name="error-code-2507"></a>Felkod: 2507

- **Meddelande**: `The folder path does not exist or is empty: ...`

- **Orsak**: inga filer finns i lagrings kontot på den angivna sökvägen.

- **Rekommendation**: sökvägen till mappen måste innehålla de körbara filer som du vill köra.

### <a name="error-code-2508"></a>Felkod: 2508

- **Meddelande**: `There are duplicate files in the resource folder.`

- **Orsak**: flera filer med samma namn finns i olika undermappar i folderPath.

- **Rekommendation**: sammanslagen mappstruktur för anpassade aktiviteter under folderPath. Om du behöver bevara mappstrukturen zip-filerna och extrahera dem i Azure Batch med hjälp av ett zippa-kommando.
   
   Använd till exempel `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>Felkod: 2509

- **Meddelande**: `Batch url ... is invalid; it must be in Uri format.`

- **Orsak**: batch-URL: er måste likna `https://mybatchaccount.eastus.batch.azure.com`

- **Rekommendation**: se fel beskrivningen.

### <a name="error-code-2510"></a>Felkod: 2510

- **Meddelande**: `An error occurred while sending the request.`

- **Orsak**: batch-URL: en är ogiltig.

- **Rekommendation**: verifiera batch-URL: en.
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-206"></a>Felkod: 206

- **Meddelande**: `The batch ID for Spark job is invalid. Please retry your job.`

- **Orsak**: det uppstod ett internt problem med tjänsten som orsakade felet.

- **Rekommendation**: det här problemet kan vara tillfälligt. Försök utföra jobbet igen om en stund.

### <a name="error-code-207"></a>Felkod: 207

- **Meddelande**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI.`

- **Orsak**: det uppstod ett internt fel vid försök att fastställa regionen från det primära lagrings kontot.

- **Rekommendation**: försök med ett annat lagrings utrymme. 

### <a name="error-code-208"></a>Felkod: 208

- **Meddelande**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again.`

- **Orsak**: det uppstod ett internt fel vid försök att läsa tjänstens huvud namn eller instansiera MSI-autentiseringen.

- **Rekommendation**: Överväg att tillhandahålla ett huvud namn för tjänsten som har behörighet att skapa ett HDInsight-kluster i den angivna prenumerationen och försök igen. Kontrol lera att [hanterade identiteter är korrekt](../hdinsight/hdinsight-managed-identities.md)konfigurerade.

### <a name="error-code-2300"></a>Felkod: 2300

- **Meddelande**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **Orsak**: fel meddelandet innehåller ett meddelande som liknar `The remote name could not be resolved.` . Den angivna kluster-URI: n kan vara ogiltig.

- **Rekommendation**: kontrol lera att klustret inte har tagits bort och att angiven URI är korrekt. När du öppnar URI i en webbläsare bör du se Ambari-ANVÄNDARGRÄNSSNITTET. Om klustret finns i ett virtuellt nätverk ska URI: n vara den privata URI: n. Öppna den genom att använda en virtuell dator (VM) som är en del av samma virtuella nätverk.

   Mer information finns i [direkt ansluta till Apache Hadoop Services](../hdinsight/hdinsight-plan-virtual-network-deployment.md#directly-connect-to-apache-hadoop-services).
 
 </br>

- **Orsak**: om fel meddelandet innehåller ett meddelande som liknar, tids gränsen uppnåddes `A task was canceled.` för jobb överföringen.

- **Rekommendation**: problemet kan vara antingen allmän HDInsight-anslutning eller nätverks anslutning. Bekräfta först att HDInsight Ambari-ANVÄNDARGRÄNSSNITTET är tillgängligt från valfri webbläsare. Kontrol lera sedan att autentiseringsuppgifterna fortfarande är giltiga.
   
   Om du använder en lokal, integrerad Runtime (IR) utför du det här steget från den virtuella datorn eller datorn där IR för egen värd är installerad. Försök sedan att skicka jobbet från Data Factory igen.

   Mer information finns i [Ambari Web UI](../hdinsight/hdinsight-hadoop-manage-ambari.md#ambari-web-ui).

 </br>

- **Orsak**: när fel meddelandet innehåller ett meddelande som liknar `User admin is locked out in Ambari` eller `Unauthorized: Ambari user name or password is incorrect` , är autentiseringsuppgifterna för HDInsight felaktigt eller har upphört att gälla.

- **Rekommendation**: korrigera autentiseringsuppgifterna och distribuera om den länkade tjänsten. Kontrol lera först att autentiseringsuppgifterna fungerar på HDInsight genom att öppna kluster-URI: n i valfri webbläsare och försök logga in. Om autentiseringsuppgifterna inte fungerar kan du återställa dem från Azure Portal.

   För ESP-kluster återställer du lösen ordet med hjälp av [lösen ords återställning via självbetjäning](../active-directory/user-help/active-directory-passwords-update-your-own-password.md).

 </br>

- **Orsak**: när fel meddelandet innehåller ett meddelande som liknar `502 - Web server received an invalid response while acting as a gateway or proxy server` , returneras det här felet av HDInsight-tjänsten.

- **Rekommendation**: ett 502-fel inträffar ofta när Ambari-serverns process stängdes av. Du kan starta om Ambari-tjänsterna genom att starta om Head-noden.

    1. Anslut till en av noderna i HDInsight med SSH.
    1. Identifiera din aktiva Head Node-värd genom att köra `ping headnodehost` .
    1. Anslut till din aktiva Head-nod som Ambari-server på den aktiva Head-noden med SSH. 
    1. Starta om den aktiva Head-noden.

       Mer information hittar du i fel söknings dokumentationen för Azure HDInsight. Exempel:

       * [Ambari UI 502-fel](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [RpcTimeoutException för Apache Spark Thrift-Server](../hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception.md)
       * [Felsöka Felaktiga Gateway-fel i Application Gateway](../application-gateway/application-gateway-troubleshooting-502.md).

 </br>

- **Orsak**: när fel meddelandet innehåller ett meddelande som liknar `Unable to service the submit job request as templeton service is busy with too many submit job requests` eller `Queue root.joblauncher already has 500 applications, cannot accept submission of application` , skickas för många jobb till HDInsight på samma gång.

- **Rekommendation**: begränsa antalet samtidiga jobb som skickats till HDInsight. Referera till Data Factory aktivitets samtidighet om jobben skickas av samma aktivitet. Ändra utlösare så att samtidiga pipelines körs sprids över tid.

   Se [HDInsight-dokumentationen](../hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors.md) för att justera `templeton.parallellism.job.submit` som ett fel förslag.

### <a name="error-code-2301"></a>Felkod: 2301

- **Meddelande**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Orsak**: HDInsight-kluster eller-tjänsten har problem.

- **Rekommendation**: det här felet uppstår när ADF inte får något svar från HDInsight-klustret vid försök att begära status för jobbet som körs. Det här problemet kan vara i själva klustret, eller så kan HDInsight-tjänsten ha ett avbrott.

   Se fel söknings dokumentation för HDInsight på https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide eller kontakta supporten för ytterligare hjälp.

### <a name="error-code-2302"></a>Felkod: 2302

- **Meddelande**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Orsak**: jobbet skickades till HDI-klustret och misslyckades där.

- **Rekommendation**: 

 1. Kontrol lera Ambari-gränssnittet:
    1. Se till att alla tjänster fortfarande körs.
    1. Från Ambari UI kontrollerar du aviserings avsnittet på instrument panelen.
       1. Mer information om aviseringar och lösningar på aviseringar finns i [Hantera och övervaka ett kluster](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Granska ditt garn minne. Om ditt garn minne är högt kan bearbetningen av dina jobb fördröjas. Om du inte har tillräckligt med resurser för att anpassa ditt Spark-program/-jobb kan du skala upp klustret för att säkerställa att klustret har tillräckligt med minne och kärnor. 
 1. Kör ett test jobb för prov.
    1. Om du kör samma jobb på HDInsight-backend-servern, kontrollerar du att det lyckades. Exempel på körnings exempel finns i [köra MapReduce-exemplen som ingår i HDInsight](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. Om jobbet fortfarande inte kunde utföras på HDInsight kontrollerar du programmets loggar och information, som tillhandahåller stöd för:
    1. Kontrol lera om jobbet skickades till garn. Om jobbet inte skickades till garn använder du `--master yarn` .
    1. Om programmet har körts klart samlar du in start tid och slut tid för garn programmet. Om programmet inte slutförde körningen samlar du in start tid/start tid.
    1. Kontrol lera och samla in program loggen med `yarn logs -applicationId <Insert_Your_Application_ID>` .
    1. Kontrol lera och samla in garn Resource Manager-loggarna under `/var/log/hadoop-yarn/yarn` katalogen.
    1. Om dessa steg inte räcker för att lösa problemet kan du kontakta Azure HDInsight-teamet för support och tillhandahålla ovanstående loggar och tidsstämplar.

### <a name="error-code-2303"></a>Felkod: 2303

- **Meddelande**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Orsak**: jobbet skickades till HDI-klustret och misslyckades där.

- **Rekommendation**: 

 1. Kontrol lera Ambari-gränssnittet:
    1. Se till att alla tjänster fortfarande körs.
    1. Från Ambari UI kontrollerar du aviserings avsnittet på instrument panelen.
       1. Mer information om aviseringar och lösningar på aviseringar finns i [Hantera och övervaka ett kluster](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Granska ditt garn minne. Om ditt garn minne är högt kan bearbetningen av dina jobb fördröjas. Om du inte har tillräckligt med resurser för att anpassa ditt Spark-program/-jobb kan du skala upp klustret för att säkerställa att klustret har tillräckligt med minne och kärnor. 
 1. Kör ett test jobb för prov.
    1. Om du kör samma jobb på HDInsight-backend-servern, kontrollerar du att det lyckades. Exempel på körnings exempel finns i [köra MapReduce-exemplen som ingår i HDInsight](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. Om jobbet fortfarande inte kunde utföras på HDInsight kontrollerar du programmets loggar och information, som tillhandahåller stöd för:
    1. Kontrol lera om jobbet skickades till garn. Om jobbet inte skickades till garn använder du `--master yarn` .
    1. Om programmet har körts klart samlar du in start tid och slut tid för garn programmet. Om programmet inte slutförde körningen samlar du in start tid/start tid.
    1. Kontrol lera och samla in program loggen med `yarn logs -applicationId <Insert_Your_Application_ID>` .
    1. Kontrol lera och samla in garn Resource Manager-loggarna under `/var/log/hadoop-yarn/yarn` katalogen.
    1. Om dessa steg inte räcker för att lösa problemet kan du kontakta Azure HDInsight-teamet för support och tillhandahålla ovanstående loggar och tidsstämplar.

### <a name="error-code-2304"></a>Felkod: 2304

- **Meddelande**: `MSI authentication is not supported on storages for HDI activities.`

- **Orsak**: de länkade lagrings tjänsterna som används i den länkade tjänsten eller HDI-aktiviteten i HDInsight (HDI) konfigureras med en MSI-autentisering som inte stöds.

- **Rekommendation**: ange fullständiga anslutnings strängar för lagrings konton som används i HDI länkade tjänst-eller HDI-aktivitet.

### <a name="error-code-2305"></a>Felkod: 2305

- **Meddelande**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Orsak**: anslutnings informationen för HDI-klustret är felaktig, den angivna användaren har inte behörighet att utföra den begärda åtgärden, eller så har HDInsight-tjänsten problem med att svara på begär Anden från ADF.

- **Rekommendation**: kontrol lera att användar informationen är korrekt och att AMBARI-gränssnittet för HDI-klustret kan öppnas i en webbläsare från den virtuella datorn där IR är installerat (för en lokal IR-anslutning) eller kan öppnas från vilken dator som helst (för Azure IR).

### <a name="error-code-2306"></a>Felkod: 2306

- **Meddelande**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Orsak**: JSON-filen som angavs för skript åtgärden är ogiltig.

- **Rekommendation**: fel meddelandet bör hjälpa till att identifiera problemet. Åtgärda JSON-konfigurationen och försök igen.

   Mer information finns [i länkad Azure HDInsight-tjänst på begäran](./compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .

### <a name="error-code-2310"></a>Felkod: 2310

- **Meddelande**: `Failed to submit Spark job. Error: '%message;'`

- **Orsak**: ADF försökte skapa en batch i ett Spark-kluster med livy API (livy/batch), men tog emot ett fel.

- **Rekommendation**: åtgärda problemet genom att följa fel meddelandet. Om det inte finns tillräckligt med information för att lösa problemet kan du kontakta HDI-teamet och ge dem batch-ID och jobb-ID, som du hittar i aktiviteten kör utdata på sidan för automatisk övervakning. Om du vill felsöka ytterligare samlar du in den fullständiga loggen för batch-jobbet.

   Mer information om hur du samlar in den fullständiga loggen finns i [Hämta den fullständiga loggen för ett batch-jobb](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2312"></a>Felkod: 2312

- **Meddelande**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Orsak**: det gick inte att utföra jobbet på HDInsight Spark-klustret.

- **Rekommendation**: Följ länkarna i sidan aktivitets körnings utdata på sidan ADF-övervakning för att felsöka körningen på HDInsight Spark-klustret. Kontakta HDInsight support team för ytterligare hjälp.

   Mer information om hur du samlar in den fullständiga loggen finns i [Hämta den fullständiga loggen för ett batch-jobb](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2313"></a>Felkod: 2313

- **Meddelande**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Orsak**: gruppen togs bort i HDInsight Spark-klustret.

- **Rekommendation**: Felsöka batchar i HDInsight Spark-klustret. Kontakta HDInsight-supporten om du behöver mer hjälp. 

   Mer information om hur du samlar in den fullständiga loggen finns i [Hämta den fullständiga loggen för ett batch-jobb](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)och dela den fullständiga loggen med HDInsight-support för ytterligare hjälp.

### <a name="error-code-2328"></a>Felkod: 2328

- **Meddelande**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Orsak**: fel meddelandet ska visa information om vad som gått fel.

- **Rekommendation**: fel meddelandet bör hjälpa till att felsöka problemet.

### <a name="error-code-2329"></a>Felkod: 2329

- **Meddelande**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Orsak**: fel meddelandet ska visa information om vad som gått fel.

- **Rekommendation**: fel meddelandet bör hjälpa till att felsöka problemet.

### <a name="error-code-2331"></a>Felkod: 2331

- **Meddelande**: `The file path should not be null or empty.`

- **Orsak**: den angivna fil Sök vägen är tom.

- **Rekommendation**: Ange en sökväg till en fil som finns.

### <a name="error-code-2340"></a>Felkod: 2340

- **Meddelande**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Orsak**: den länkade tjänsten HDInsightOnDemand stöder inte körning via SelfHosted IR.

- **Rekommendation**: välj en Azure IR och försök igen.

### <a name="error-code-2341"></a>Felkod: 2341

- **Meddelande**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Orsak**: den angivna URL-adressen är inte i rätt format.

- **Rekommendation**: åtgärda kluster-URL: en och försök igen.

### <a name="error-code-2342"></a>Felkod: 2342

- **Meddelande**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Orsak**: antingen är de angivna autentiseringsuppgifterna fel för klustret, eller så uppstod ett nätverks konfigurations-eller anslutnings problem, eller så har IR-anslutningen problem att ansluta till klustret.

- **Rekommendation**: 
    1. Kontrol lera att autentiseringsuppgifterna är korrekta genom att öppna HDInsight-klustrets Ambari-användargränssnitt i en webbläsare.
    1. Om klustret är i Virtual Network (VNet) och en IR med egen värd används, måste HDI-URL: en vara den privata URL: en i virtuella nätverk, och ska ha "-int" i listan efter kluster namnet.
    
       Till exempel ändra `https://mycluster.azurehdinsight.net/` till `https://mycluster-int.azurehdinsight.net/` . Observera `-int` efter `mycluster` , men före `.azurehdinsight.net`
    1. Om klustret finns i VNet används det lokala IR-nätverket, och den privata URL: en användes, och anslutningen fortfarande misslyckades, men anslutningen fortfarande misslyckades, men den virtuella datorn där IR har installerats hade problem med att ansluta till HDI. 
    
       Anslut till den virtuella datorn där IR är installerat och öppna Ambari-ANVÄNDARGRÄNSSNITTET i en webbläsare. Använd den privata URL: en för klustret. Den här anslutningen bör fungera från webbläsaren. Om den inte gör det kontaktar du HDInsight support team för ytterligare hjälp.
    1. Om IR med egen värd inte används, ska HDI-klustret vara tillgängligt offentligt. Öppna Ambari-ANVÄNDARGRÄNSSNITTET i en webbläsare och kontrol lera att den öppnas. Om det finns några problem med klustret eller tjänsterna på den kontaktar du HDInsight support team för att få hjälp.

       Den HDI kluster-URL som används i den länkade ADF-tjänsten måste vara tillgänglig för ADF IR (lokal eller Azure) för att test anslutningen ska kunna skickas och för att körningen ska fungera. Det här läget kan verifieras genom att öppna URL: en från en webbläsare, antingen från en virtuell dator eller från en offentlig dator.

### <a name="error-code-2343"></a>Felkod: 2343

- **Meddelande**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Orsak**: antingen användar namnet eller lösen ordet är tomt.

- **Rekommendation**: ange rätt autentiseringsuppgifter för att ansluta till HDI och försök igen.

### <a name="error-code-2345"></a>Felkod: 2345

- **Meddelande**: `Failed to read the content of the hive script. Error: '%message;'`

- **Orsak**: skript filen finns inte eller också kunde inte ADF ansluta till platsen för skriptet.

- **Rekommendation**: kontrol lera att skriptet finns och att den associerade länkade tjänsten har rätt autentiseringsuppgifter för en anslutning.

### <a name="error-code-2346"></a>Felkod: 2346

- **Meddelande**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Orsak**: ADF försökte upprätta en Open Database Connectivity-anslutning (ODBC) till HDI-klustret och den misslyckades med ett fel.

- **Rekommendation**: 

   1. Bekräfta att du har konfigurerat ODBC/Java Database Connectivity-anslutningen (JDBC) korrekt.
      1. Om du använder samma virtuella nätverk för JDBC kan du få den här anslutningen från:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. För att se till att du har rätt JDBC-konfiguration, se [fråga Apache Hive via JDBC-drivrutinen i HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. För Open Database (ODB), se [Självstudier: fråga Apache Hive med ODBC och PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) för att kontrol lera att du har rätt konfiguration. 
   1. Kontrol lera att Hiveserver2, Hive Metaarkiv och Hiveserver2 Interactive är aktiva och fungerar. 
   1. Kontrol lera användar gränssnittet för Ambari (UI):
      1. Se till att alla tjänster fortfarande körs.
      1. I Ambari-ANVÄNDARGRÄNSSNITTET kontrollerar du aviserings avsnittet på instrument panelen.
         1. Mer information om aviseringar och lösningar på aviseringar finns i [Hantera och övervaka ett kluster ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Om dessa steg inte räcker för att lösa problemet kan du kontakta Azure HDInsight-teamet.

### <a name="error-code-2347"></a>Felkod: 2347

- **Meddelande**: `Hive execution through ODBC failed with error message '%message;'.`

- **Orsak**: ADF skickade Hive-skriptet för körning till HDI-klustret via ODBC-anslutning och skriptet har misslyckats på HDI.

- **Rekommendation**: 

   1. Bekräfta att du har konfigurerat ODBC/Java Database Connectivity-anslutningen (JDBC) korrekt.
      1. Om du använder samma virtuella nätverk för JDBC kan du få den här anslutningen från:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. För att se till att du har rätt JDBC-konfiguration, se [fråga Apache Hive via JDBC-drivrutinen i HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. För Open Database (ODB), se [Självstudier: fråga Apache Hive med ODBC och PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) för att kontrol lera att du har rätt konfiguration. 
   1. Kontrol lera att Hiveserver2, Hive Metaarkiv och Hiveserver2 Interactive är aktiva och fungerar. 
   1. Kontrol lera användar gränssnittet för Ambari (UI):
      1. Se till att alla tjänster fortfarande körs.
      1. I Ambari-ANVÄNDARGRÄNSSNITTET kontrollerar du aviserings avsnittet på instrument panelen.
         1. Mer information om aviseringar och lösningar på aviseringar finns i [Hantera och övervaka ett kluster ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Om dessa steg inte räcker för att lösa problemet kan du kontakta Azure HDInsight-teamet.

### <a name="error-code-2348"></a>Felkod: 2348

- **Meddelande**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Orsak**: de länkade tjänst egenskaperna för lagring har inte angetts korrekt.

- **Rekommendation**: endast fullständiga anslutnings strängar stöds i den länkade huvud lagrings tjänsten för HDI-aktiviteter. Kontrol lera att du inte använder MSI-auktoriseringar eller program.

### <a name="error-code-2350"></a>Felkod: 2350

- **Meddelande**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Orsak**: autentiseringsuppgifterna som angavs för att ansluta till lagrings platsen där filerna ska placeras är felaktiga, eller så finns inte filerna där.

- **Rekommendation**: det här felet uppstår när ADF förbereder för HDI-aktiviteter och försöker kopiera filer till huvud lagringen innan jobbet skickas till HDI. Kontrol lera att filerna finns på den angivna platsen och att lagrings anslutningen är korrekt. Eftersom ADF HDI-aktiviteter inte stöder MSI-autentisering på lagrings konton relaterade till HDI-aktiviteter, kontrollerar du att de länkade tjänsterna har fullständiga nycklar eller använder Azure Key Vault.

### <a name="error-code-2351"></a>Felkod: 2351

- **Meddelande**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Orsak**: filen finns inte på den angivna sökvägen.

- **Rekommendation**: kontrol lera om filen verkligen finns och att den länkade tjänsten med anslutnings information som pekar på den här filen har rätt autentiseringsuppgifter.

### <a name="error-code-2352"></a>Felkod: 2352

- **Meddelande**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Orsak**: egenskaperna för den länkade fil lagrings tjänsten har inte angetts korrekt.

- **Rekommendation**: kontrol lera att egenskaperna för den länkade fil lagrings tjänsten är korrekt konfigurerade.

### <a name="error-code-2353"></a>Felkod: 2353

- **Meddelande**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Orsak**: de länkade tjänst egenskaperna för skript lagring har inte angetts korrekt.

- **Rekommendation**: kontrol lera att egenskaperna för den länkade skript lagrings tjänsten är korrekt konfigurerade.

### <a name="error-code-2354"></a>Felkod: 2354

- **Meddelande**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Orsak**: den länkade tjänst typen för lagring stöds inte av aktiviteten.

- **Rekommendation**: kontrol lera att den valda länkade tjänsten har en av de typer som stöds för aktiviteten. HDI-aktiviteter stöder AzureBlobStorage-och AzureBlobFSStorage-länkade tjänster.

   Mer information finns i [jämföra lagrings alternativ för användning med Azure HDInsight-kluster](../hdinsight/hdinsight-hadoop-compare-storage-options.md)

### <a name="error-code-2355"></a>Felkod: 2355

- **Meddelande**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Orsak**: det angivna värdet för `commandEnvironment` är felaktigt.

- **Rekommendation**: kontrol lera att det angivna värdet liknar:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Kontrol lera också att varje variabel bara visas i listan en gång.

### <a name="error-code-2356"></a>Felkod: 2356

- **Meddelande**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **Orsak**: det angivna värdet för `commandEnvironment` är felaktigt.

- **Rekommendation**: kontrol lera att det angivna värdet liknar:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Kontrol lera också att varje variabel bara visas i listan en gång.

### <a name="error-code-2357"></a>Felkod: 2357

- **Meddelande**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Orsak**: de angivna autentiseringsuppgifterna är felaktiga.

- **Rekommendation**: kontrol lera att anslutnings informationen i ADLS gen 1 är länkad till tjänsten och kontrol lera att test anslutningen lyckas.

### <a name="error-code-2358"></a>Felkod: 2358

- **Meddelande**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Orsak**: det angivna värdet för den obligatoriska egenskapen `TimeToLive` har ett ogiltigt format. 

- **Rekommendation**: uppdatera värdet till det föreslagna intervallet och försök igen.

### <a name="error-code-2359"></a>Felkod: 2359

- **Meddelande**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Orsak**: det angivna värdet för egenskapen `roles` är ogiltigt.

- **Rekommendation**: uppdatera värdet till ett av förslagen och försök igen.

### <a name="error-code-2360"></a>Felkod: 2360

- **Meddelande**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Orsak**: den angivna anslutnings strängen för `HCatalogLinkedService` är ogiltig.

- **Rekommendation**: uppdatera värdet till en korrekt Azure SQL-anslutningssträng och försök igen.

### <a name="error-code-2361"></a>Felkod: 2361

- **Meddelande**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Orsak**: det gick inte att skapa klustret och ADF fick inget fel tillbaka från HDInsight-tjänsten.

- **Rekommendation**: öppna Azure Portal och försök hitta HDI-resursen med det angivna namnet och kontrol lera etablerings statusen. Kontakta HDInsight support team för ytterligare hjälp.

### <a name="error-code-2362"></a>Felkod: 2362

- **Meddelande**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Orsak**: den tillhandahållna ytterligare lagringen var inte Azure Blob Storage.

- **Rekommendation**: tillhandahåll ett Azure Blob Storage-konto som ett ytterligare lagrings utrymme för en länkad HDInsight-tjänst på begäran.

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>SSL-fel vid länkad ADF-tjänst med HDInsight ESP-kluster

- **Meddelande**: `Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **Orsak**: problemet är förmodligen relaterat till system Trust Store.

- **Lösning**: du kan gå till sökvägen **Microsoft integration RUNTIME\4.0\SHARED\ODBC Drivers\Microsoft Hive ODBC Driver\lib** och öppna DriverConfiguration64.exe för att ändra inställningen.

    ![Avmarkera Använd system förtroende lager](./media/connector-troubleshoot-guide/system-trust-store-setting.png)

## <a name="web-activity"></a>Webbaktivitet

### <a name="error-code-2128"></a>Felkod: 2128

- **Meddelande**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Orsak**: det här problemet beror på nätverks anslutningen, ett DNS-fel, en verifiering av Server certifikat eller en tids gräns.

- **Rekommendation**: kontrol lera att slut punkten som du försöker trycka på svarar på begär Anden. Du kan använda verktyg som **Fiddler/Postman**.

### <a name="error-code-2108"></a>Felkod: 2108

- **Meddelande**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Orsak**: begäran misslyckades på grund av ett underliggande problem, t. ex. nätverks anslutning, ett DNS-fel, en verifiering av Server certifikat eller en tids gräns.

- **Rekommendation**: Använd Fiddler/Postman för att verifiera begäran.

#### <a name="more-details"></a>Mer information
Så här använder du **Fiddler** för att skapa en http-session för det övervakade webb programmet:

1. Ladda ned, installera och öppna [Fiddler](https://www.telerik.com/download/fiddler).

1. Om ditt webb program använder https går du till **verktyg**  >  **Fiddler alternativ**  >  **https**.

   1. På fliken HTTPS väljer du både **avbilda https ansluter** och **dekryptera HTTPS-trafik**.

      ![Fiddler-alternativ](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Om ditt program använder TLS/SSL-certifikat, lägger du till Fiddler-certifikatet på enheten.

   Gå till: **verktyg**  >  **Fiddler alternativ**  >  **https**  >  **åtgärder**  >  **Exportera rot certifikat till Skriv bordet**.

1. Inaktivera hämtning genom att gå till **fil**  >  **hämtnings trafik**. Eller tryck på **F12**.

1. Rensa webbläsarens cacheminne så att alla cachelagrade objekt tas bort och måste laddas ned igen.

1. Skapa en begäran:

1. Välj fliken **Composer** .

   1. Ange HTTP-metod och URL.
 
   1. Om det behövs kan du lägga till rubriker och en brödtext i begäran.

   1. Välj **Kör**.

1. Aktivera trafik insamlingen igen och slutför den felande transaktionen på sidan.

1. Gå **till:**  >  **Spara**  >  **alla sessioner**.

Mer information finns i [komma igång med Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="general"></a>Allmänt

### <a name="activity-stuck-issue"></a>Problem med aktivitet som fastnat

När du observerar att aktiviteten körs mycket längre än vad som händer i din normala körning utan att det uppstår något steg, kan det hända att den fastnar. Du kan försöka att avbryta och försöka se om det hjälper. Om det är en kopierings aktivitet kan du lära dig mer om prestanda övervakning och fel sökning från [Felsök kopierings aktivitets prestanda](copy-activity-performance-troubleshooting.md). om det är ett data flöde, lär du dig att [mappa data flöden prestanda](concepts-data-flow-performance.md) -och justerings guide.

### <a name="payload-is-too-large"></a>Nytto lasten är för stor

**Fel meddelande:**`The payload including configurations on activity/dataSet/linked service is too large. Please check if you have settings with very large value and try to reduce its size.`

**Orsak:** Nytto lasten för varje aktivitets körning omfattar aktivitets konfigurationen, de associerade data uppsättningarna och de länkade tjänst (s)-konfigurationerna om några, och en liten del av system egenskaperna som genereras per aktivitets typ. Begränsningen av denna nytto Last storlek är 896 KB som anges i avsnittet [Data Factory gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) .

**Rekommendation:** Du nådde förmodligen den här gränsen eftersom du skickar in en eller flera stora parameter värden från överordnad aktivitet eller externt, särskilt om du överför faktiska data mellan aktiviteter i kontroll flödet. Kontrol lera om du kan minska storleken på stora parameter värden eller finjustera din pipeline-logik för att undvika att överföra sådana värden mellan aktiviteter och hantera dem i aktiviteten i stället.

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

* [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-videor](https://azure.microsoft.com/resources/videos/index/)
* [Sidan Microsoft Q&en fråga](/answers/topics/azure-data-factory.html)
