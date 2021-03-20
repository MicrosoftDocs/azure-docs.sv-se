---
title: 'Bevara fil-ACL: er, attribut och tidsstämplar med Azure Data Box'
description: 'ACL: er, tidsstämplar och attribut bevaras under data kopiering via SMB till Azure Data Box. Kopiera metadata med Windows-och Linux-verktyg för data kopiering.'
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: e8df77356b6b5b1b40e2abd772e13c2e811413ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950320"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Bevara fil-ACL: er, attribut och tidsstämplar med Azure Data Box

Med Azure Data Box kan du bevara åtkomst kontrol listor (ACL: er), tidsstämplar och filattribut när du skickar data till Azure. I den här artikeln beskrivs de metadata som du kan överföra när du kopierar data till Data Box-enhet via SMB (Server Message Block) för att ladda upp det till Azure Files. 

Vissa steg finns för att kopiera metadata med Windows-och Linux-verktyg för data kopiering. Metadata bevaras inte vid överföring av data till Blob Storage.

I den här artikeln kallas ACL: er, tidsstämplar och filattribut som överförs gemensamt som *metadata*.

## <a name="transferred-metadata"></a>Överförda metadata

Följande metadata överförs när data från Data Box-enhet laddas upp till Azure Files.

#### <a name="timestamps"></a>Tidsstämplar

Följande tidsstämplar överförs:
- SkapadTid
- LastWriteTime

Följande tidsstämpel överförs inte:
- LastAccessTime
  
#### <a name="file-attributes"></a>Filattribut

Filattribut för både filer och kataloger överförs om inget annat anges.

Följande filattribut överförs:
- FILE_ATTRIBUTE_READONLY (endast fil)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (endast katalog)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (endast fil)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

Följande filattribut överförs inte:
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
Skrivskyddade attribut i kataloger överförs inte.

#### <a name="acls"></a>ACL: er

Alla ACL: er för kataloger och filer som du kopierar till din Data Box-enhet över SMB kopieras och överförs. Överföringar inkluderar både DACL-listor (DACL) och system-ACL: er (SACL). För Linux överförs endast Windows NT ACL: er.

ACL: er överförs inte under data kopior över Network File System (nter) och när du använder tjänsten data kopiering för att överföra dina data. Data kopierings tjänsten läser data direkt från dina resurser och kan inte läsa ACL: er.

Även om ditt data kopierings verktyg inte kopierar ACL: er, överförs standard-ACL: er för kataloger och filer till Azure Files. Standard-ACL: erna har behörigheter för det inbyggda administratörs kontot, SYSTEM kontot och det SMB-resurs användar konto som användes för att montera och kopiera data i Data Box-enhet.

Åtkomst kontrol listorna innehåller säkerhets beskrivningar med följande egenskaper: ACL: er, ägare, grupp, SACL.

Överföring av ACL: er är aktiverat som standard. Du kanske vill inaktivera den här inställningen i det lokala webb gränssnittet på din Data Box-enhet. Mer information finns i [använda det lokala webb gränssnittet för att administrera data Box-enhet och data Box Heavy](./data-box-local-web-ui-admin.md).

> [!NOTE]
> Filer med ACL: er som innehåller strängar för villkorlig åtkomst kontroll post (ACE) kopieras inte. Detta är ett känt problem. Undvik detta genom att kopiera filerna till den Azure Files resursen manuellt genom att montera resursen och sedan använda ett kopierings verktyg som stöder kopiering av ACL: er.

## <a name="copying-data-and-metadata"></a>Kopiera data och metadata

Om du vill överföra ACL: er, tidsstämplar och attribut för dina data använder du följande procedurer för att kopiera data till Data Box-enhet. 

### <a name="windows-data-copy-tool"></a>Verktyget Windows data kopiering

Om du vill kopiera data till din Data Box-enhet via SMB använder du ett SMB-kompatibelt fil kopierings verktyg som `robocopy` . Följande exempel kommando kopierar alla filer och kataloger, och överför metadata tillsammans med data.

När du använder `/copyall` `/dcopy:DAT` alternativet eller kontrollerar du att den nödvändiga behörigheten för säkerhets kopiering inte är inaktive rad. Mer information finns i [använda det lokala webb gränssnittet för att administrera data Box-enhet och data Box Heavy](./data-box-local-web-ui-admin.md). 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

var

|Alternativ |Beskrivning |
|------------------- | ----- |
|`/copyall` |Kopierar alla attribut.|
|`/e`      |Kopierar under kataloger, inklusive tomma kataloger.         |
|`/dcopy:DAT`  |Kopierar data, attribut och tidsstämplar. Obs! alternativet/DCOPY: DAT måste användas för att överföra `CreationTime` kataloger. |
|`/r:3`    |Anger 3 återförsök vid misslyckade kopior.         |
|`/w:60`   |Anger en vänte tid på 60 sekunder mellan återförsök.         |
|`/is`     |Innehåller samma filer.         |
|`/nfl`    |Loggar inte fil namn.         |
|`/ndl`    |Loggar inte katalog namn.        |
|`/np`     |Visar inte förloppet för kopierings åtgärden.         |
|`/MT:32 or 64`  |Använder multitrådning med 32 eller 64-trådar.           |
|`/fft`    |Minskar tids stämplings precisionen för alla fil system.        |
|`/log+:<LogFile>`  |Lägger till utdata till den befintliga loggfilen.|

Mer information om dessa `robocopy` parametrar finns i [Självstudier: kopiera data till Azure Data box via SMB](./data-box-deploy-copy-data.md)

### <a name="linux-data-copy-tool"></a>Verktyget Linux data kopiering

Överföring av metadata i Linux är en två stegs process. Först kopierar du data källan med hjälp av ett verktyg som `rsync` , som inte kopierar metadata. När du har kopierat data kan du kopiera metadata med hjälp av ett verktyg som `smbcacls` eller `cifsacl` . 

Följande exempel kommandon gör det första steget och kopierar data med hjälp av `rsync` . 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>Nästa steg

- [Kopiera data till Azure Data Box via SMB](./data-box-deploy-copy-data.md)