---
title: Spåra och logga Azure Data Box Azure Data Box Heavy händelser för export ordning | Microsoft Docs
description: Beskriver hur du spårar och loggar händelser i de olika stegen i din Azure Data Box och Azure Data Box Heavy export ordning.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 3a915ac8de83a5e183660ec4a3d05044eafff4a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94337516"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>Spårning och händelse loggning för din Azure Data Box och Azure Data Box Heavy export order

En Data Box-enhet-eller Data Box Heavys export ordning går igenom följande steg: order, konfiguration, data kopiering, retur och data radering. Som motsvarar varje steg i ordningen kan du utföra flera åtgärder för att kontrol lera åtkomsten till ordern, granska händelserna, spåra beställningen och tolka de olika loggar som genereras.

Den här artikeln beskriver i detalj vilka olika mekanismer eller verktyg som är tillgängliga för att spåra och granska export order för Data Box-enhet eller Data Box Heavy. Informationen i den här artikeln gäller både för Data Box-enhet och Data Box Heavy. I följande avsnitt gäller alla referenser till Data Box-enhet även för Data Box Heavy.

I följande tabell visas en översikt över de Data Box-enhet stegen för att exportera order och de verktyg som är tillgängliga för att spåra och granska beställningen under varje steg.

| Data Box-enhet export order fas       | Verktyg för att spåra och granska                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Skapa order               | [Konfigurera åtkomst kontroll på ordern via Azure RBAC](#set-up-access-control-on-the-order) <br> [Aktivera utförlig logg i ordern](#enable-verbose-log-in-the-order)                                                    |
| Bearbetad beställning            | [Spåra beställningen](#track-the-order) genom <ul><li> Azure Portal </li><li> Frakt bär Vågs webbplats </li><li>E-postmeddelanden</ul> |
| Konfigurera enhet              | Autentiseringsuppgifter [för åtkomst till](#query-activity-logs-during-setup) inloggnings uppgifter för enhet              |
| Data kopiering från enhet        | [Granska kopierings loggar](#copy-log) <br> [Granska utförliga loggar](#verbose-log) innan du kopierar data            |
| Data radering från enhet   | [Visa kedja av vårdnads loggar](#get-chain-of-custody-logs-after-data-erasure) inklusive gransknings loggar och order historik                |


## <a name="set-up-access-control-on-the-order"></a>Konfigurera åtkomst kontroll på ordern

Du kan styra vem som kan komma åt din beställning när ordern först skapas. Konfigurera Azure-roller i olika scope för att kontrol lera åtkomsten till Data Box-enhets ordningen. En Azure-roll avgör typ av åtkomst – Läs-och Skriv behörighet, skrivskyddad, Läs-och skriv åtgärder för en delmängd åtgärder.

De två roller som kan definieras för den Azure Data Box tjänsten är:

- **Data Box-enhet läsare** – du får skrivskyddad åtkomst till en order som definieras av omfånget. De kan bara visa information om en order. De kan inte komma åt annan information som rör lagrings konton eller redigera beställnings informationen, till exempel adress och så vidare.
- **Data Box-enhet deltagare** – kan bara skapa en order för att överföra data till ett angivet lagrings konto *om de redan har Skriv behörighet till ett lagrings konto*. Om de inte har åtkomst till ett lagrings konto kan de inte ens skapa en Data Box-enhet ordning för att kopiera data till kontot. Den här rollen definierar inte några behörigheter för lagrings konton eller beviljar åtkomst till lagrings konton.  

Om du vill begränsa åtkomsten till en order kan du:

- Tilldela en roll på en order nivå. Användaren har bara de behörigheter som definieras av rollerna för att interagera med just den aktuella Data Box-enhet ordningen och inget annat.
- Tilldela en roll på resurs grupps nivå, användaren har åtkomst till alla Data Box-enhet beställningar inom en resurs grupp.

Mer information om föreslagen Azure RBAC-användning finns i [metod tips för Azure RBAC](../role-based-access-control/best-practices.md).

## <a name="enable-verbose-log-in-the-order"></a>Aktivera utförlig logg i ordern

När du placerar en export ordning för Data Box-enhet har du möjlighet att aktivera insamling av en utförlig logg. Här är ordnings skärmen där du kan aktivera utförlig logg:

![Välj export alternativ](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

När du väljer alternativet **Inkludera utförlig logg** genereras en utförlig logg fil när du kopierar data från ditt Azure Storage-konto. Den här loggen innehåller en lista över alla filer som har exporter ATS.

Mer information om export order finns i [skapa en export ordning för data Box-enhet](data-box-deploy-export-ordered.md)

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

- Varje inloggning i Data Box-enhet loggas i real tid. Den här informationen är dock bara tillgänglig i [kedjan av vårdnads gransknings loggar](#chain-of-custody-audit-logs) när beställningen har slutförts.

## <a name="view-logs-during-data-copy"></a>Visa loggar under data kopiering

Innan du kopierar data från Data Box-enhet kan du hämta och granska *kopierings loggen* och *utförlig loggen* för de data som kopierats till data Box-enhet. Dessa loggar skapas när data kopieras från ditt lagrings konto i Azure till din Data Box-enhet. 

### <a name="copy-log"></a>Kopiera logg

Innan du kopierar data från Data Box-enhet hämtar du kopierings loggen från sidan **Anslut och kopiera** .

Här är ett exempel på utdata från *kopierings loggen* när det inte fanns några fel och alla filer kopierades under data kopieringen från Azure till data Box-enhet enheten.

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
Här är ett exempel på utdata när *kopierings loggen* innehåller fel och vissa av filerna inte kunde kopieras från Azure.

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

Du har följande alternativ för att exportera dessa filer: 

- Du kan överföra de filer som inte kunde kopieras över nätverket. 
- Om datastorleken var större än den användbara enhetskapaciteten sker en partiell kopiering, och alla filer som inte kopierades visas i den här loggen. Du kan använda den här loggen som indata-XML för att skapa en ny Data Box-order och sedan kopiera över dessa filer.

### <a name="verbose-log"></a>Utförlig logg

Den *utförliga loggen* innehåller en lista över alla filer som exporterades från Azure Storage-kontot. Loggen innehåller även filstorlek och beräkning av kontrollsumma.

Utförlig loggen innehåller informationen i följande format:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Här är ett exempel på utdata från utförlig logg. 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

Utförliga loggar kopieras också till Azure Storage-kontot. Som standard skrivs loggar till en behållare med namnet `copylog` . Loggarna lagras med följande namngivnings konvention:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Kopierings logg Sök vägen visas också på bladet **Översikt** för portalen.

<!-- add a screenshot-->

Du kan använda dessa loggar för att kontrol lera att filer som har kopierats från Azure matchar de data som kopierades till din lokala server. 

Använd den detaljerade logg filen:

- För att verifiera mot de faktiska namnen och antalet filer som kopierats från Data Box-enhet.
- För att verifiera mot de faktiska storlekarna för filerna.
- För att kontrol lera att *crc64* motsvarar en sträng som inte är noll. En CRC-beräkning (cyklisk redundans) görs under exporten från Azure. CRCs från exporten och efter att data har kopierats från Data Box-enhet till en lokal server kan jämföras. Ett CRC-fel indikerar att motsvarande filer inte kunde kopieras korrekt.


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Hämta kedja av vårdnads loggar efter radering av data

När data har raderats från Data Box-enhet diskar enligt rikt linjerna för NIST SP 800-88 Revision 1 är kedjan med vårdnads loggar tillgänglig. Dessa loggar innehåller en kedja av vårdnads gransknings loggar och order historik. STRUKTURLISTE-eller manifest filerna kopieras också med gransknings loggarna.

### <a name="chain-of-custody-audit-logs"></a>Kedja av vårdnads gransknings loggar

Kedjan med invårdnads gransknings loggar innehåller information om att starta och komma åt resurser på Data Box-enhet eller Data Box Heavy när den är utanför Azure-datacenter. Loggarna finns på: `storage-account/azuredatabox-chainofcustodylogs`

Här är ett exempel på gransknings loggen från en Data Box-enhet:

```output
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
