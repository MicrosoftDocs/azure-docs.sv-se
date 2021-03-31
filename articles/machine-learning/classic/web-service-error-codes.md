---
title: 'ML Studio (klassisk): REST API fel koder – Azure'
description: De här fel koderna kan returneras av en åtgärd på en Azure Machine Learning-webbtjänst.
keywords: ''
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: c5a457a673500c542dd6130d5cfadd73ab677f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518359"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klassisk) REST API felkoder

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Följande felkoder kan returneras av en åtgärd på en Azure Machine Learning Studio (klassisk)-webb tjänst.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP-status kod 400)
 
Ogiltigt argument har angetts.
 
Den här fel klassen innebär att ett argument som angetts någonstans var ogiltigt. Detta kan vara en autentiseringsuppgift eller en plats för Azure Storage till något som skickas till webb tjänsten. Se fel fältet "kod" i avsnittet "information" för att diagnostisera vilket särskilt argument som är ogiltigt.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| BadParameterValue | Det angivna parametervärdet uppfyller inte parameter regeln för parametern |
| BadSubscriptionId | Det prenumerations-ID som används för att räkna med är inte det som finns i resursen |
| BadVersionCall | En ogiltig versions parameter skickades under API-anropet: {0} . Kontrol lera API-hjälp sidan för att skicka rätt version och försök igen. |
| BatchJobInputsNotSpecified | Följande obligatoriska indatatyper har inte angetts med begäran: {0} . Kontrol lera att alla indata har angetts och försök igen. |
| BatchJobInputsTooManySpecified | Begäran angav fler indata än vad som har definierats i tjänsten. Lista över godkända indatatyper: {0} . Kontrol lera att alla indata har angetts korrekt och försök igen. |
| BlobNameTooLong | Azure Blob Storage-sökvägen som tillhandahölls för diagnostiska utdata är för lång: {0} . Förkorta sökvägen och försök igen. |
| BlobNotFound | Det gick inte att komma åt den angivna Azure-blobben {0} .  Azure-fel meddelande: {1} . |
| ContainerIsEmpty | Inget namn har angetts för Azure Storage-behållaren. Ange ett giltigt behållar namn och försök igen. |
| ContainerSegmentInvalid | Ogiltigt behållar namn. Ange ett giltigt behållar namn och försök igen. |
| ContainerValidationFailed | Det gick inte att verifiera BLOB-behållaren. fel meddelande: {0} . |
| DataTypeNotSupported | Den angivna data typen stöds inte. Ange giltiga data typer och försök igen. |
| DuplicateInputInBatchCall | Batch-begäran är ogiltig. Det går inte att ange både enstaka och flera inmatade samtidigt. Ta bort ett av dessa objekt från begäran och försök igen. |
| ExpiryTimeInThePast | Den angivna förfallo tiden är i det förflutna: {0} . Ange en framtida förfallo tid i UTC och försök igen. Ange förfallo tid till NULL om du aldrig vill upphöra att gälla. |
| IncompleteSettings | Inställningarna för diagnostik är ofullständiga. |
| InputBlobRelativeLocationInvalid | Inget Azure Storage-BLOB-namn har angetts. Ange ett giltigt BLOB-namn och försök igen. |
| InvalidBlob | Ogiltig BLOB-specifikation för BLOB: {0} . Kontrol lera att anslutnings strängen/relativ sökväg eller specifikationer för SAS-token är korrekta och försök igen. |
| InvalidBlobConnectionString | Anslutnings strängen som angetts för en av blobarna för indata/utdata är ogiltig: {0} . Korrigera detta och försök igen. |
| InvalidBlobExtension | BLOB-referensen: {0} har ett fil namns tillägg som är ogiltigt eller saknas. Fil namns tillägg som stöds för den här utdatatypen är: " {1} ". |
| InvalidInputNames | Ett eller flera ogiltiga tjänst indatatyper har angetts i begäran: {0} . Mappa indata till rätt tjänst indata och försök igen. |
| InvalidOutputOverrideName | Ogiltigt namn på åsidosättning av utdata: {0} . Tjänsten har ingen utmatnings nod med det här namnet. Ange rätt namn på utdatafilen för att åsidosätta (Skift läges känslighet gäller). |
| InvalidQueryParameter | Ogiltig frågeparameter {0} . {1} |
| MissingInputBlobInformation | BLOB-information för Azure Storage saknas. Ange en giltig anslutnings sträng och relativ sökväg eller URI och försök igen. |
| MissingJobId | Inget jobb-ID har angetts. Ett jobb-ID returneras när ett jobb skickas för första gången. Kontrol lera att jobb-ID: t är korrekt och försök igen. |
| MissingKeys | Inga nycklar har angetts eller så har en primär eller sekundär nyckel inte angetts. |
| MissingModelPackage | Inget modell paket-ID eller modell paket har angetts. Ange ett giltigt modell paket-ID eller modell paket och försök igen. |
| MissingOutputOverrideSpecification | Begäran saknar BLOB-specifikationen för åsidosättning av utdata {0} . Ange en giltig BLOB-plats med begäran eller ta bort utmatnings specifikationen om Ingen åsidosättning av platsen önskas. |
| MissingRequestInput | Webb tjänsten förväntar sig en indata, men inga indata angavs. Se till att giltiga indata tillhandahålls baserat på de publicerade indataportarna i modellen och försök igen. |
| MissingRequiredGlobalParameters | Alla nödvändiga webb tjänst parametrar har inte angetts. Kontrol lera att parametrarna som förväntades för modulerna är korrekta och försök igen. |
| MissingRequiredOutputOverrides | När du anropar en krypterad tjänst slut punkt är det obligatoriskt att överföra åsidosättningar av utdata för alla tjänstens utdata. Åsidosättningar som saknas för den här tiden för dessa utdata: {0} |
| MissingWebServiceGroupId | Inget ID för webb tjänst grupp har angetts. Ange ett giltigt ID för webb tjänst gruppen och försök igen. |
| MissingWebServiceId | Inget webb tjänst-ID har angetts. Ange ett giltigt ID för webb tjänst och försök igen. |
| MissingWebServicePackage | Inget webb tjänst paket har tillhandahållits. Ange ett giltigt webb tjänst paket och försök igen. |
| MissingWorkspaceId | Inget arbetsyte-ID har angetts. Ange ett giltigt arbetsyte-ID och försök igen. |
| ModelConfigurationInvalid | Ogiltig modell konfiguration i modell paketet. Se till att modell konfigurationen innehåller utdata slut punkt (er), standard fel slut punkt och utgångs punkt för STDAV och försök igen. |
| ModelPackageIdInvalid | Ogiltigt ID för modell paket. kontrol lera att modell paketets ID är korrekt och försök igen. |
| RequestBodyInvalid | Ingen begär ande text har angetts eller fel vid avserialisering av begär ande texten. |
| RequestIsEmpty | Ingen begäran har tillhandahållits. Ange en giltig begäran och försök igen. |
| UnexpectedParameter | Oväntade parametrar har angetts. Kontrol lera att alla parameter namn är rättstavade, endast förväntade parametrar skickas och försök igen. |
| UnknownError | Okänt fel. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Det går inte att ändra kraven för samtidiga begär Anden för {0} webb tjänsten. |
| WebServiceIdInvalid | Ogiltigt webb tjänst-ID har angetts. Webb tjänst-ID ska vara ett giltigt GUID. |
| WebServiceTooManyConcurrentRequestRequirement | Det går inte att ange krav för samtidiga begär anden till mer än {0} . |
| WebServiceTypeInvalid | En ogiltig webb tjänst typ har angetts. Kontrol lera att den giltiga webb tjänst typen är korrekt och försök igen. Giltiga webb tjänst typer: {0} . |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP-status kod 400)
 
Ogiltigt användar argument har angetts.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| InputMismatchError | Indata stämmer inte överens med indata-portens schema. |
| InputParseError | Parsning av indatamängds vektor misslyckades.  Kontrol lera att inmatnings vektorn har rätt antal kolumner och data typer.  Ytterligare information: {0} . |
| MissingRequiredGlobalParameters | Parameter (er) som förväntas av webb tjänsten saknas. Kontrol lera att alla nödvändiga parametrar som förväntas av webb tjänsten är korrekta och försök igen. |
| UnexpectedParameter | Kontrol lera att endast de nödvändiga parametrarna som förväntas av webb tjänsten har skickats och försök igen. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (HTTP-status kod 400)
 
Begäran är ogiltig i den aktuella kontexten.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| CannotStartJob | Det går inte att starta jobbet eftersom det är i {0} tillstånd. |
| IncompatibleModel | Modellen är inte kompatibel med den begärda versionen. Begär ande versionen stöder bara DataTables-utdata. |
| MultipleInputsNotAllowed | Modellen tillåter inte flera indata. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP-status kod 400)
 
Module-körningen påträffade ett internt biblioteks fel.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP-status kod 400)
 
Det uppstod ett fel i modulen.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP-status kod 400)
 
Ogiltigt webb tjänst paket. Kontrol lera att det angivna webb tjänst paketet är korrekt och försök igen.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| FormatError | Webb tjänst paketet är felaktigt. Information {0} |
| RuntimesError | Webb tjänst paketets diagram är ogiltigt. Information {0} |
| ValidationError | Webb tjänst paketets diagram är ogiltigt. Information {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Obehörig (HTTP-statuskod 401)
 
Begäran har inte behörighet att komma åt resursen.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| AdminRequestUnauthorized | Behörighet saknas |
| ManagementRequestUnauthorized | Behörighet saknas |
| ScoreRequestUnauthorized | Ogiltiga autentiseringsuppgifter har angetts. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP-status kod 404)
 
Resursen hittades inte.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| ModelPackageNotFound | Det gick inte att hitta modell paketet. Kontrol lera att modell paketets ID är korrekt och försök igen. |
| WebServiceIdNotFoundInWorkspace | Webb tjänsten under den här arbets ytan hittades inte. Det finns ett matchnings fel mellan webServiceId och workspaceId. Kontrol lera att den angivna webb tjänsten tillhör arbets ytan och försök igen. |
| WebServiceNotFound | Webb tjänsten hittades inte. Kontrol lera att webb tjänstens ID är korrekt och försök igen. |
| WorkspaceNotFound | Arbets ytan hittades inte. Kontrol lera att arbetsyte-ID: t är korrekt och försök igen. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP-status kod 408)
 
Åtgärden kunde inte slutföras inom den tillåtna tiden.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| RequestCanceled | Begäran avbröts av klienten. |
| ScoreRequestTimeout | Tids gränsen nåddes för körnings förfrågan. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (HTTP-status kod 409)
 
Resursen finns redan.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Ogiltigt namn på utmatnings parameter. Prova att använda modulen metadata Editor för att byta namn på kolumner och försök igen. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (HTTP-status kod 413)
 
Modellen överskred den tilldelade minnes kvoten.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| OutOfMemoryLimit | Modellen förbrukade mer minne än vad som krävs för den. Högsta tillåtna minne för modellen är {0} MB. Kontrol lera om det finns problem i din modell. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (HTTP-status kod 500)
 
Ett internt fel inträffade i körningen.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | Container processen kraschade med systemfel |
| ContainerProcessTerminatedWithUnknownError | Container processen kraschade med ett okänt fel |
| ContainerValidationFailed | Det gick inte att verifiera BLOB-behållaren. fel meddelande: {0} . |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Inga argument har angetts. Kontrol lera att giltiga argument har angetts och försök igen. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Port-ID = {0} har en datatyp som inte stöds: {1} . |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Swagger-genereringen misslyckades, information: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Okänd jobb status kod {0} . |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, information: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (HTTP-status kod 500)
 
Ett internt fel inträffade i körningen. System ont om minne. Försök igen.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP-status kod 500)
 
Ogiltigt modell paket. Kontrol lera att det angivna modell paketet är korrekt och försök igen.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (HTTP-status kod 500)
 
Ogiltigt webb tjänst paket. Kontrol lera att det angivna webb paketet är korrekt och försök igen.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| ModuleError | Webb tjänst paketets diagram är ogiltigt. Information {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (HTTP-status kod 503)
 
Begäran kan inte köras eftersom behållarna initieras.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (HTTP-status kod 503)
 
Tjänsten är inte tillgänglig för tillfället.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| NoMoreResources | Det finns inga tillgängliga resurser för begäran. |
| RequestThrottled | Begäran begränsades för {0} slut punkten. Maximal samtidighet för slut punkten är {1} . |
| TooManyConcurrentRequests | För många samtidiga förfrågningar har skickats. |
| TooManyHostsBeingInitialized | För många värdar initieras på samma gång. Överväg att begränsa/försöka igen. |
| TooManyHostsBeingInitializedPerModel | För många värdar initieras på samma gång. Överväg att begränsa/försöka igen. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP-status kod 504)
 
Åtgärden kunde inte slutföras inom den tillåtna tiden.
 
| Felkod | Användar meddelande |
| ---------- |--------------|
| BackendInitializationTimeout | Det gick inte att slutföra initieringen av webb tjänsten inom den tillåtna tiden. |
| BackendScoreTimeout | Det gick inte att utföra körningen av webb tjänst begär Anden inom den tillåtna tiden. |
