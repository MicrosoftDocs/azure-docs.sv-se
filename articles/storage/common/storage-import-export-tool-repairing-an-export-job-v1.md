---
title: Reparera ett export jobb i Azure import/export-v1 | Microsoft Docs
description: Lär dig hur du reparerar ett export jobb som har skapats och körs med tjänsten Azure import/export.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 0731848e1ff187afb6e9f607516dd74b6c16de9b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520489"
---
# <a name="repairing-an-export-job"></a>Reparera ett exportjobb
När ett export jobb har slutförts kan du köra Microsoft Azure Import/Export-verktyget lokalt för att:  
  
1.  Ladda ned filer som Azure import/export-tjänsten inte kunde exportera.  
  
2.  Verifiera att filerna på enheten har exporter ATS korrekt.  
  
Du måste ha anslutning till Azure Storage för att kunna använda den här funktionen.  
  
Kommandot för att reparera ett import jobb är **RepairExport**.

## <a name="repairexport-parameters"></a>RepairExport-parametrar

Följande parametrar kan anges med **RepairExport**:  
  
|Parameter|Beskrivning|  
|---------------|-----------------|  
|**/r: <RepairFile\>**|Krävs. Sökväg till reparations filen som spårar förloppet för reparationen och gör att du kan återuppta en avbruten reparation. Varje enhet måste ha en och bara en reparations fil. När du startar en reparation för en specifik enhet skickar du sökvägen till en reparations fil som ännu inte finns. Om du vill återuppta en avbruten reparation bör du skicka namnet på en befintlig reparations fil. Ange alltid den reparations fil som motsvarar mål enheten.|  
|**/logdir: <LogDirectory\>**|Valfritt. Logg katalogen. Utförliga loggfiler skrivs till den här katalogen. Om ingen logg katalog anges kommer den aktuella katalogen att användas som logg katalog.|  
|**/d: <TargetDirectory\>**|Krävs. Den katalog som ska verifieras och repare ras. Den här katalogen är vanligt vis rot katalogen på export enheten, men det kan också vara en nätverks fil resurs som innehåller en kopia av de exporterade filerna.|  
|**/BK: <BitLockerKey\>**|Valfritt. Ange BitLocker-nyckeln om du vill att verktyget ska låsa upp en krypterad plats där de exporterade filerna lagras.|  
|**/SN: <StorageAccountName\>**|Krävs. Namnet på lagrings kontot för export jobbet.|  
|**/sk: <StorageAccountKey\>**|**Krävs** om och endast om en behållar-säkerhetsassociationer inte har angetts. Konto nyckeln för lagrings kontot för export jobbet.|  
|**/CSAS: <behållare\>**|**Krävs** om och endast om lagrings konto nyckeln inte har angetts. Behållar-SAS för åtkomst till de blobar som är associerade med export jobbet.|  
|**/CopyLogFile: <DriveCopyLogFile\>**|Krävs. Sökvägen till logg filen för enhets kopian. Filen genereras av tjänsten Windows Azure import/export och kan laddas ned från blob-lagringen som är kopplad till jobbet. Kopierings logg filen innehåller information om misslyckade blobbar eller filer som ska repare ras.|  
|**/ManifestFile: <DriveManifestFile\>**|Valfritt. Sökvägen till export enhetens manifest fil. Den här filen genereras av tjänsten Windows Azure import/export och lagras på export enheten. Alternativt, i en BLOB i det lagrings konto som är associerat med jobbet.<br /><br /> Innehållet i filerna på export enheten verifieras med de MD5-hashar som finns i den här filen. Skadade filer laddas ned och skrivs om till mål katalogerna.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Använda RepairExport-läge för att korrigera misslyckade exporter  
Du kan använda Azure import/export-verktyget för att hämta filer som inte gick att exportera. Kopierings logg filen innehåller en lista över filer som inte kunde exporteras.  
  
Orsakerna till export fel inkluderar följande möjligheter:  
  
-   Skadade enheter  
  
-   Lagrings konto nyckeln ändrades under överförings processen  
  
Om du vill köra verktyget i **RepairExport** -läge måste du först ansluta enheten som innehåller de exporterade filerna till datorn. Kör sedan verktyget Azure import/export och ange sökvägen till den enheten med `/d` parametern. Du måste också ange sökvägen till enhetens kopierings logg fil som du laddade ned. Följande kommando rads exempel kör verktyget för att reparera filer som inte exporterades:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Följande exempel är en kopierings logg fil som visar att det inte gick att exportera ett block i bloben:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Kopierings logg filen indikerar att ett fel uppstod när tjänsten Windows Azure import/export hämtade en av blobens block till filen på export enheten. De andra komponenterna i filen har hämtats och fil längden angavs korrekt. I det här fallet öppnar verktyget filen på enheten, hämtar blocket från lagrings kontot och skriver det till fil intervallet som börjar från offset 65536 med längden 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Verifiera enhets innehåll med hjälp av RepairExport  
Du kan också använda Azure import/export med alternativet **RepairExport** för att kontrol lera att innehållet på enheten är korrekt. Manifest filen på varje export enhet innehåller MD5s för innehållet i enheten.  
  
Azure import/export-tjänsten kan också spara manifest filerna till ett lagrings konto under export processen. Platsen för manifest filen är tillgänglig via åtgärden [Hämta jobb](/rest/api/storageimportexport/jobs) när jobbet har slutförts. För ytterligare information om formatet för en enhets manifest fil, se [import/export-tjänstens manifest fil format](storage-import-export-file-format-metadata-and-properties.md).  
  
I följande exempel visas hur du kör Azure import/export-verktyget med parametrarna **/ManifestFile** och **/CopyLogFile** :  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
I följande exempel visas en manifest fil:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
När reparations processen har slutförts läser verktyget igenom varje fil som refereras i manifest filen och kontrollerar filens integritet med MD5-hashar. För manifestet ovan kommer det att gå igenom följande komponenter.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Alla komponenter som inte verifierar kommer att laddas ned av verktyget och skrivas om till samma fil på enheten.  
  
## <a name="next-steps"></a>Nästa steg
 
* [Konfigurera verktyget Azure import/export](storage-import-export-tool-setup-v1.md)   
* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Granska jobbstatus med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparera ett importjobb](storage-import-export-tool-repairing-an-import-job-v1.md)
