---
title: Spåra och logga Azure Data Box Azure Data Box Heavy händelser för import ordningen | Microsoft Docs
description: Beskriver hur du spårar och loggar händelser i de olika stegen i Azure Data Box och Azure Data Box Heavy import ordning.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: a9304936f746b82b59550d62e8b60a9e0035d188
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147926"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-import-order"></a>Spårning och händelse loggning för din Azure Data Box och Azure Data Box Heavy import ordning

En Data Box-enhet-eller Data Box Heavy import ordning går igenom följande steg: order, installation, data kopiering, retur, uppladdning till Azure och bekräfta och data radering. Som motsvarar varje steg i ordningen kan du utföra flera åtgärder för att kontrol lera åtkomsten till ordern, granska händelserna, spåra beställningen och tolka de olika loggar som genereras.

I följande tabell visas en sammanfattning av Data Box-enhet eller Data Box Heavy import ordnings steg och de verktyg som är tillgängliga för att spåra och granska beställningen under varje steg.

| Data Box-enhet importera ordnings steg       | Verktyg för att spåra och granska                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Skapa order               | [Konfigurera åtkomst kontroll på ordern via Azure RBAC](#set-up-access-control-on-the-order)                                                    |
| Bearbetad beställning            | [Spåra beställningen](#track-the-order) genom <ul><li> Azure Portal </li><li> Frakt bär Vågs webbplats </li><li>E-postmeddelanden</ul> |
| Konfigurera enhet              | Autentiseringsuppgifter [för åtkomst till](#query-activity-logs-during-setup) inloggnings uppgifter för enhet                                              |
| Data kopiering till enhet        | [Visa *error.xml* filer](#view-error-log-during-data-copy) för data kopiering                                                             |
| Förbereda för att skicka            | [Kontrol lera BOM-filerna](#inspect-bom-during-prepare-to-ship) eller manifest filerna på enheten                                      |
| Data uppladdning till Azure       | [Granska kopierings loggar](#review-copy-log-during-upload-to-azure) för fel under data uppladdning i Azure Data Center                         |
| Data radering från enhet   | [Visa kedja av vårdnads loggar](#get-chain-of-custody-logs-after-data-erasure) inklusive gransknings loggar och order historik                |

Den här artikeln beskriver i detalj vilka olika mekanismer eller verktyg som är tillgängliga för att spåra och granska Data Box-enhet eller Data Box Heavy import ordning. Informationen i den här artikeln gäller både Data Box-enhet och Data Box Heavy import order. I följande avsnitt gäller alla referenser till Data Box-enhet även för Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Konfigurera åtkomst kontroll på ordern

Du kan styra vem som kan komma åt din beställning när ordern först skapas. Konfigurera Azure-roller i olika scope för att kontrol lera åtkomsten till Data Box-enhets ordningen. En Azure-roll avgör typ av åtkomst – Läs-och Skriv behörighet, skrivskyddad, Läs-och skriv åtgärder för en delmängd åtgärder.

De två roller som kan definieras för den Azure Data Box tjänsten är:

- **Data Box-enhet läsare** – du får skrivskyddad åtkomst till en order som definieras av omfånget. De kan bara visa information om en order. De kan inte komma åt annan information som rör lagrings konton eller redigera beställnings informationen, till exempel adress och så vidare.
- **Data Box-enhet deltagare** – kan bara skapa en order för att överföra data till ett angivet lagrings konto *om de redan har Skriv behörighet till ett lagrings konto*. Om de inte har åtkomst till ett lagrings konto kan de inte ens skapa en Data Box-enhet ordning för att kopiera data till kontot. Den här rollen definierar inte några behörigheter för lagrings konton eller beviljar åtkomst till lagrings konton.  

Om du vill begränsa åtkomsten till en order kan du:

- Tilldela en roll på en order nivå. Användaren har bara de behörigheter som definieras av rollerna för att interagera med just den aktuella Data Box-enhet ordningen och inget annat.
- Tilldela en roll på resurs grupps nivå, användaren har åtkomst till alla Data Box-enhet beställningar inom en resurs grupp.

Mer information om föreslagen Azure RBAC-användning finns i [metod tips för Azure RBAC](../role-based-access-control/best-practices.md).

## <a name="track-the-order"></a>Spåra beställningen

Du kan spåra din beställning genom Azure Portal och via frakt bär Vågs webbplatsen. Följande mekanismer är på plats för att spåra Data Box-enhets ordningen när som helst:

- Om du vill spåra beställningen när enheten är i Azure-datacenter eller lokalt går du till **data Box-enhet beställ > översikt** i Azure Portal.

    ![Visa order status och spårning nej](media/data-box-logs/overview-view-status-1.png)

- Om du vill spåra beställningen medan enheten överförs går du till webbplatsen för regional bärvåg, till exempel UPS-webbplats i USA. Ange det spårnings nummer som är associerat med din beställning.
- Data Box-enhet skickar också e-postaviseringar varje beställnings status ändras baserat på de e-postmeddelanden som angavs när ordern skapades. En lista över alla Data Box-enhet order status finns i [Visa order status](data-box-portal-admin.md#view-order-status). Information om hur du ändrar meddelande inställningarna som är associerade med ordern finns i [Redigera meddelande information](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Fråga aktivitets loggar under installationen

- Dina Data Box-enhet kommer in i låst tillstånd. Du kan använda autentiseringsuppgifter för enheten som är tillgängliga i Azure Portal för din beställning.  

    När en Data Box-enhet har kon figurer ATS kan du behöva veta vem som har åtkomst till enhetens autentiseringsuppgifter. Om du vill ta reda på vem som har åtkomst till bladet **autentiseringsuppgifter för enhet** kan du fråga aktivitets loggarna.  Alla åtgärder som inbegriper åtkomst till **enhets information > inloggnings** bladet loggas i aktivitets loggarna som `ListCredentials` åtgärd.

    ![Frågeaktivitetsloggar](media/data-box-logs/query-activity-log-1.png)

- Varje inloggning i Data Box-enhet loggas i real tid. Den här informationen är dock bara tillgänglig i [gransknings loggarna](#audit-logs) när beställningen har slutförts.

## <a name="view-error-log-during-data-copy"></a>Visa fel logg vid data kopiering

Under data kopieringen till Data Box-enhet eller Data Box Heavy genereras en felfil om det finns problem med data som kopieras.

### <a name="errorxml-file"></a>Error.xml fil

Kontrol lera att kopierings jobben har avslut ATS utan fel. Om det uppstår fel under kopierings processen kan du hämta loggarna från sidan **Anslut och kopiera** .

- Om du kopierade en fil som inte är 512 byte-justerad till en mapp för hanterad disk på din Data Box-enhet laddas inte filen upp som en sid-blob till ditt lagrings konto för lagring. Du kommer att se ett fel i loggarna. Ta bort filen och kopiera en fil som är 512 byte-justerad.
- Om du har kopierat en VHDX, eller en dynamisk virtuell hård disk eller en differentierande virtuell hård disk (dessa filer stöds inte), visas ett fel i loggarna.

Här är ett exempel på *error.xml* för olika fel vid kopiering till hanterade diskar.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Här är ett exempel på *error.xml* för olika fel när du kopierar till Page blobbar.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Här är ett exempel på *error.xml* för olika fel vid kopiering till block-blobar.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Här är ett exempel på *error.xml* för olika fel när du kopierar till Azure Files.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

I vart och ett av ovanstående fall löser du felen innan du fortsätter till nästa steg. Mer information om de fel som tas emot under data kopieringen till Data Box-enhet via SMB-eller NFS-protokoll finns i [felsöka data Box-enhet och data Box Heavy problem](data-box-troubleshoot.md). Information om fel som tas emot under data kopieringen till Data Box-enhet via REST finns i [felsöka data Box-enhet Blob Storage-problem](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Inspektera struktur listan under förbereda för leverans

Under förbereda för att leverera skapas en lista över filer som kallas struktur lista (BOM) eller manifest fil.

- Använd den här filen för att kontrol lera mot de faktiska namnen och antalet filer som kopierats till Data Box-enhet.
- Använd den här filen för att kontrol lera mot de faktiska storlekarna för filerna.
- Kontrol lera att *crc64* motsvarar en sträng som inte är noll. <!--A null value for crc64 indicates that there was a reparse point error)-->

Om du vill ha mer information om de fel som har tagits emot vid förberedelse av, går du till [felsöka data Box-enhet och data Box Heavy problem](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>Struktur lista eller manifest fil

STRUKTUR listan eller manifest filen innehåller en lista över alla filer som har kopierats till den Data Box-enhet enheten. STRUKTUR filen innehåller fil namn och motsvarande storlekar samt kontroll summan. En separat STRUKTURLISTEVERSION skapas för block-blobar, Page blobbar Azure Files, för kopiering via REST-API: er, och för kopieringen till hanterade diskar på Data Box-enhet. Du kan hämta BOM-filerna från det lokala webb gränssnittet på enheten när du förbereder att leverera.

Filerna finns också på Data Box-enhet-enheten och överförs till det associerade lagrings kontot i Azure-datacentret.

### <a name="bom-file-format"></a>STRUKTURLISTE-filformat

STRUKTURLISTE-eller manifest filen har följande allmänna format:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Här är ett exempel på ett manifest som genereras när data kopierades till Block Blob-resursen på Data Box-enhet.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

STRUKTUR-eller manifest filerna kopieras också till Azure Storage-kontot. Du kan använda STRUKTURLISTE-eller manifest filen för att kontrol lera att filer som laddats upp till Azure matchar de data som kopierades till Data Box-enhet.

## <a name="review-copy-log-during-upload-to-azure"></a>Granska kopierings loggen under uppladdning till Azure

Under data överföringen till Azure skapas en kopierings logg.

### <a name="copy-log"></a>Kopiera logg

För varje order som bearbetas skapar Data Box-enhet tjänsten kopierings loggen i det associerade lagrings kontot. Kopierings loggen innehåller det totala antalet filer som har överförts och antalet filer som fel uppstod under data kopieringen från Data Box-enhet till ditt Azure Storage-konto.

En CRC-beräkning (cyklisk redundans) görs under uppladdningen till Azure. CRCs från data kopian och efter data överföringen jämförs. Ett CRC-fel indikerar att motsvarande filer inte kunde laddas upp.

Som standard skrivs loggar till en behållare med namnet `copylog` . Loggarna lagras med följande namngivnings konvention:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Kopierings logg Sök vägen visas också på bladet **Översikt** för portalen.

![Sökväg till bladet kopiera logg översikt vid slutförd](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Överföringen har slutförts 

I följande exempel beskrivs det allmänna formatet för en kopierings logg för en Data Box-enhet uppladdning som har slutförts:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Uppladdningen slutfördes med fel 

Överföring till Azure kan också slutföras med fel.

![Sökväg till bladet för att kopiera loggen i översikten när det slutfördes med fel](media/data-box-logs/copy-log-path-2.png)

Här är ett exempel på en kopierings logg där uppladdningen slutfördes med fel:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>Uppladdningen slutfördes med varningar

Överföring till Azure slutförs med varningar om dina data hade behållare/BLOB/fil namn som inte stämmer överens med namngivnings konventionerna i Azure och namnen ändrades för att överföra data till Azure.

![Sökväg till bladet för att kopiera loggen när den har slutförts med varningar](media/data-box-logs/copy-log-path-3.png)

Här är ett exempel på en kopierings logg där behållarna som inte överensstämmer med namngivnings konventionerna för Azure bytte namn under data överföringen till Azure.

De nya unika namnen för behållare är i formatet `DataBox-GUID` och data för behållaren placeras i den nya omdöpta behållaren. Kopierings loggen anger det gamla och det nya behållar namnet för container.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Här är ett exempel på en kopierings logg där blobbar eller filer som inte överensstämmer med namngivnings konventionerna för Azure har bytt namn under data överföringen till Azure. De nya BLOB-eller fil namnen konverteras till SHA256-sammandrag av den relativa sökvägen till behållaren och överförs till sökvägen baserat på typ av mål. Målet kan vara block blobbar, Page blobbar eller Azure Files.

`copylog`Anger den gamla och den nya blobben eller fil namnet och sökvägen i Azure.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Hämta kedja av vårdnads loggar efter radering av data

När data har raderats från Data Box-enhet diskar enligt rikt linjerna för NIST SP 800-88 Revision 1 är kedjan med vårdnads loggar tillgänglig. Loggarna innehåller gransknings loggarna och order historiken. STRUKTURLISTE-eller manifest filerna kopieras också med gransknings loggarna.

### <a name="audit-logs"></a>Granskningsloggar

Gransknings loggar innehåller information om hur du kan sätta igång och få åtkomst till resurser på Data Box-enhet eller Data Box Heavy när de är utanför Azure-datacenter. Loggarna finns på: `storage-account/azuredatabox-chainofcustodylogs`

Här är ett exempel på gransknings loggen från en Data Box-enhet:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Ladda ned beställningshistorik

Order historik är tillgänglig i Azure Portal. Om ordern är klar och enhets rensningen (data radering från diskarna) är klar går du till din enhets ordning och navigerar till **order information**. Alternativet **Ladda ned beställningshistorik** är tillgängligt. Mer information finns i [Hämta order historik](data-box-portal-admin.md#download-order-history).

Om du bläddrar igenom order historiken visas:

- Information om operatörs spårning för enheten.
- Händelser med *SecureErase* -aktivitet. Dessa händelser motsvarar radering av data på disken.
- Data Box-enhet logg länkar. Sök vägarna till *gransknings loggarna*, *kopierings loggar* och *BOM* -filer visas.

Här är ett exempel på loggen för order historik från Azure Portal:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöker problem på data Box-enhet och data Box Heavy](data-box-troubleshoot.md).
