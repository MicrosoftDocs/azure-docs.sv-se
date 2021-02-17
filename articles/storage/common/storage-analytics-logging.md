---
title: Loggning med Azure-lagringsanalys
description: Använd Lagringsanalys för att logga information om Azure Storage förfrågningar. Se vilka begär Anden som loggas, Hur loggar lagras, hur du aktiverar lagrings loggning med mera.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: f1d254eecc41ebef690b4fc9f8294bee5a368ae4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570032"
---
# <a name="azure-storage-analytics-logging"></a>Analysloggning i Azure Storage

Lagringsanalys loggar detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst. Den här informationen kan användas för att övervaka enskilda begäranden och för att diagnostisera problem med en lagringstjänst. Begär Anden loggas med bästa möjliga ansträngning.

> [!NOTE]
> Vi rekommenderar att du använder Azure Storage loggar i Azure Monitor i stället för Lagringsanalys loggar. Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. Den här för hands versionen aktiverar loggar för blobbar (som innehåller Azure Data Lake Storage Gen2), filer, köer och tabeller. Mer information finns i följande artiklar:
>
> - [Övervaka Azure-Blob Storage](../blobs/monitor-blob-storage.md)
> - [Övervaknings Azure Files](../files/storage-files-monitoring.md)
> - [Övervaka Azure-Queue Storage](../queues/monitor-queue-storage.md)
> - [Övervaka Azure Table Storage](../tables/monitor-table-storage.md)

 Lagringsanalysloggning är inte aktiverat som standard för ditt lagringskonto. Du kan aktivera det i [Azure Portal](https://portal.azure.com/) eller med hjälp av PowerShell eller Azure CLI. Steg-för-steg-anvisningar finns i [Aktivera och hantera Azure-lagringsanalys loggar (klassisk)](manage-storage-analytics-logs.md). 

Du kan också aktivera Lagringsanalys loggar via programmering via REST API eller klient biblioteket. Använd egenskaperna [Hämta BLOB service](/rest/api/storageservices/Blob-Service-REST-API), [Hämta egenskaper för Queue Service](/rest/api/storageservices/Get-Queue-Service-Properties)och [Hämta tabell tjänst egenskaper](/rest/api/storageservices/Get-Table-Service-Properties) för att aktivera Lagringsanalys för varje tjänst. Om du vill se ett exempel som aktiverar Lagringsanalys loggar med hjälp av .NET, se [Aktivera loggar](manage-storage-analytics-logs.md)

 Logg poster skapas endast om det finns begär Anden som görs mot tjänst slut punkten. Om ett lagrings konto till exempel har aktivitet i dess BLOB-slutpunkt men inte i dess tabell-eller Queue-slutpunkter, skapas bara loggar som rör Blob Service.

> [!NOTE]
>  Lagringsanalysloggning är för närvarande endast tillgängligt för blob-, kö- och tabelltjänsterna. Lagringsanalys loggning är också tillgängligt för [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) -konton med Premium-prestanda. Men det är inte tillgängligt för General-Purpose v2-konton med förstklassiga prestanda.

## <a name="requests-logged-in-logging"></a>Begär Anden som loggats i loggning
### <a name="logging-authenticated-requests"></a>Logga autentiserade begär Anden

 Följande typer av autentiserade begäranden loggas:

- Lyckade begäranden
- Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel
- Begäranden med en signatur för delad åtkomst (SAS), inklusive misslyckade och lyckade begäranden
- Begäranden om analysdata

  Begär Anden som görs av Lagringsanalys, till exempel när loggen skapas eller tas bort loggas inte. En fullständig lista över de loggade data som dokumenteras finns i avsnitten [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [Lagringsanalys logg format](/rest/api/storageservices/storage-analytics-log-format) .

### <a name="logging-anonymous-requests"></a>Logga anonyma begär Anden

 Följande typer av anonyma begär Anden loggas:

- Lyckade begäranden
- Serverfel
- Timeout-fel för både klienten och servern
- Misslyckade GET-begäranden med felkoden 304 (har inte ändrats)

  Alla andra misslyckade anonyma begär Anden loggas inte. En fullständig lista över de loggade data som dokumenteras finns i avsnitten [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) och [Lagringsanalys logg format](/rest/api/storageservices/storage-analytics-log-format) .

## <a name="how-logs-are-stored"></a>Så här lagras loggar

Alla loggar lagras i block-blobar i en behållare med namnet `$logs` , som skapas automatiskt när Lagringsanalys har Aktiver ATS för ett lagrings konto. `$logs`Behållaren finns i BLOB-namnrymden för lagrings kontot, till exempel: `http://<accountname>.blob.core.windows.net/$logs` . Det går inte att ta bort den här behållaren när Lagringsanalys har Aktiver ATS, men innehållet kan tas bort. Om du använder ditt sökverktyg för att navigera till behållaren direkt visas alla blobar som innehåller dina loggnings data.

> [!NOTE]
>  `$logs`Behållaren visas inte när en åtgärd för container listan utförs, till exempel list containers-åtgärden. Det måste nås direkt. Du kan till exempel använda List-blobar-åtgärden för att få åtkomst till blobarna i `$logs` behållaren.

När förfrågningar loggas kommer Lagringsanalys att överföra mellanliggande resultat som block. Med jämna mellanrum kommer Lagringsanalys att bekräfta dessa block och göra dem tillgängliga som en blob. Det kan ta upp till en timme innan loggdata visas i blobarna i **$logs** container eftersom lagrings tjänsten tömmer logg skribenterna. Det kan finnas dubbla poster för loggar som skapats i samma timme. Du kan avgöra om en post är en dubblett genom att kontrol lera **RequestId** och **Åtgärds** nummer.

Om du har en stor mängd loggdata med flera filer per timme kan du använda BLOB-metadata för att avgöra vilka data som loggen innehåller genom att undersöka fälten för BLOB-metadata. Detta är också användbart eftersom det ibland kan vara en fördröjning när data skrivs till loggfilerna: BLOB-metadata ger en mer korrekt indikation av BLOB-innehållet än BLOB-namnet.

Med de flesta verktyg för lagrings surfning kan du visa metadata för blobbar. Du kan också läsa informationen med hjälp av PowerShell eller program mässigt. Följande PowerShell-kodfragment är ett exempel på hur du filtrerar listan över logg-blobar efter namn för att ange en tid, och efter metadata för att identifiera enbart de loggar som innehåller **Skriv** åtgärder.  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'blob/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Information om hur du registrerar blobbar program mässigt finns i [räkna upp BLOB-resurser](/rest/api/storageservices/Enumerating-Blob-Resources) och [Ange och hämta egenskaper och metadata för BLOB-resurser](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources).  

### <a name="log-naming-conventions"></a>Logg namngivnings konventioner

 Varje logg skrivs i följande format:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 I följande tabell beskrivs varje attribut i logg namnet:

|Attribut|Beskrivning|
|---------------|-----------------|
|`<service-name>`|Namnet på lagrings tjänsten. Till exempel: `blob` , `table` eller `queue`|
|`YYYY`|Årtalet med fyra siffror för loggen. Exempelvis: `2011`|
|`MM`|Den två siffrorna i månaden för loggen. Exempelvis: `07`|
|`DD`|Den två siffriga dagen för loggen. Exempelvis: `31`|
|`hh`|Den två siffriga timmen som anger start timmen för loggarna, i UTC-format i 24 timmar. Exempelvis: `18`|
|`mm`|Det två siffer numret som anger start minuten för loggarna. **Obs:**  Värdet stöds inte i den aktuella versionen av Lagringsanalys och värdet är alltid `00` .|
|`<counter>`|En noll-baserad räknare med sex siffror som anger antalet logg-blobar som har genererats för lagrings tjänsten under en tids period. Räknaren startar vid `000000` . Exempelvis: `000001`|

 Följande är ett fullständigt exempel på logg namn som kombinerar exemplen ovan:

 `blob/2011/07/31/1800/000001.log`

 Följande är en exempel-URI som kan användas för att komma åt loggen ovan:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 När en lagrings förfrågan loggas motsvarar logg namnet i timmen när den begärda åtgärden slutfördes. Om till exempel en GetBlob-begäran har slutförts med 6:17.30 på 7/31/2011, skrivs loggen med följande prefix: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Logga metadata

 Alla log-blobbar lagras med metadata som kan användas för att identifiera vilka loggnings data som bloben innehåller. I följande tabell beskrivs varje attribut för metadata:

|Attribut|Beskrivning|
|---------------|-----------------|
|`LogType`|Beskriver om loggen innehåller information som rör Läs-, Skriv-eller borttagnings åtgärder. Det här värdet kan innehålla en typ eller en kombination av alla tre, avgränsade med kommatecken.<br /><br /> Exempel 1: `write`<br /><br /> Exempel 2: `read,write`<br /><br /> Exempel 3: `read,write,delete`|
|`StartTime`|Den tidigaste tiden för en post i loggen i form av `YYYY-MM-DDThh:mm:ssZ` . Exempelvis: `2011-07-31T18:21:46Z`|
|`EndTime`|Den senaste tiden för en post i loggen i form av `YYYY-MM-DDThh:mm:ssZ` . Exempelvis: `2011-07-31T18:22:09Z`|
|`LogVersion`|Versionen av logg formatet.|

 I följande lista visas kompletta exempel-metadata med hjälp av ovanstående exempel:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`


## <a name="next-steps"></a>Nästa steg

* [Aktivera och hantera Azure-lagringsanalys loggar (klassisk)](manage-storage-analytics-logs.md)
* [Lagringsanalys logg format](/rest/api/storageservices/storage-analytics-log-format)
* [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Lagringsanalys mått (klassisk)](storage-analytics-metrics.md)
