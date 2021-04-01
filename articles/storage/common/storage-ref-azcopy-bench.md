---
title: AzCopy bänk | Microsoft Docs
description: Den här artikeln innehåller referensinformation för AzCopy bänk kommando.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c1028d0a4a458746c08fd6fa4f16aa952d9962a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282015"
---
# <a name="azcopy-benchmark"></a>AzCopy-prestandatest

Kör ett prestanda mått genom att ladda upp eller ladda ned test data till eller från ett angivet mål. För uppladdningar genereras test data automatiskt.

Benchmark-kommandot kör samma process som "Copy", förutom att: 

  - I stället för både käll-och mål parametrarna tar benchmark bara en. Detta är BLOB-behållaren, Azure Files resurs eller Azure Data Lake Storage Gen2 fil system som du vill ladda upp eller ladda ned från.

  - Parametern "mode" beskriver huruvida AzCopy ska testa uppladdningar eller ladda ned från det angivna målet. Giltiga värden är upload och Download. Standardvärdet är ' upload '.

  - För att ladda upp benchmarks, beskrivs nytto lasten av kommando rads parametrar, som styr hur många filer som genereras automatiskt och hur viktiga filerna ska vara. Generations processen sker helt i minnet. Disken används inte.

  - För hämtningar består nytto lasten av de filer som redan finns på källan. (Se exemplet nedan om hur du genererar testfiler om det behövs).
  
  - Endast några av de valfria parametrarna som är tillgängliga för kopierings kommandot stöds.
  
  - Ytterligare diagnostik mäts och rapporteras.
  
  - För uppladdningar är standard beteendet att ta bort överförda data i slutet av test körningen.  För hämtningar sparas inte data lokalt.

Benchmark-läget justerar sig automatiskt till antalet parallella TCP-anslutningar som ger maximalt data flöde. Den kommer att visas i slutet av den här siffran. För att förhindra autojustering ställer du in AZCOPY_CONCURRENCY_VALUE-miljövariabeln på ett angivet antal anslutningar. 

Alla typer av vanliga autentiseringar stöds. Den bekvämaste metoden för benchmarking-överföring är dock vanligt vis att skapa en tom behållare med en SAS-token och använda SAS-autentisering. (Hämtnings läget kräver att en uppsättning test data finns i mål behållaren.)

## <a name="examples"></a>Exempel

```azcopy
azcopy benchmark [destination] [flags]
```

Kör ett benchmark-test med standard parametrar (lämpligt för benchmarking-nätverk upp till 1 Gbit/s):

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Kör ett benchmark-test som överför 100-filer, varje 2-GiB i storlek: (lämpligt för benchmarking i ett snabbt nätverk, till exempel 10 Gbit/s): '

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Kör ett benchmark-test men Använd 50 000-filer, varje 8 MiB i storlek och beräkna deras MD5-hashar (på samma sätt som `--put-md5` flaggan gör detta i kopierings kommandot). Syftet med `--put-md5` när benchmarking är att testa om MD5-beräkningen påverkar data flödet för det valda fil antalet och storleken:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Kör ett benchmark-test som laddar ned befintliga filer från ett mål

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Kör en uppladdning som inte tar bort de överförda filerna. (De här filerna kan sedan fungera som nytto last för ett nedladdnings test)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Alternativ

**--BLOB-Type** -strängen definierar BLOB-typen vid målet. Används för att tillåta benchmarking av olika BLOB-typer. Identiskt med samma namngivna parameter i kopierings kommandot (standard identifiering).

**--block-size-MB** float Använd den här block storleken (anges i MIB). Standardvärdet beräknas automatiskt baserat på fil storlek. Decimal tal är tillåtna – till exempel 0,25. Identiskt med samma-namngivna parameter i kopierings kommandot.

**--kontrol lera längd**  Kontrol lera längden på en fil på målet efter överföringen. Om det finns ett matchnings fel mellan källan och målet markeras överföringen som misslyckad. (standard sant)

**--Delete-test-data**  Om värdet är True tas benchmark-data bort i slutet av benchmark-körningen.  Ange värdet till false om du vill behålla data vid målet, till exempel för att använda den för manuella tester utanför benchmark-läge (standard sant).

**--fil-Count-** uint.  Antalet automatiskt genererade datafiler som ska användas (standard 100).

**--Hjälp**  Hjälp för bänk

**--sträng på loggnivå** definierar loggens utförlighet för logg filen, tillgängliga nivåer: info (alla begär Anden/svar), varning (långsamma svar), fel (endast misslyckade förfrågningar) och ingen (inga utgående loggar). (standard information)

**--läge** strängen definierar om AzCopy ska testa överföringar eller nedladdningar från det här målet. Giltiga värden är upload och Download. Standard alternativet är upload. (standard överföring)

**--antal-Folders** -uint om större än 0, skapa mappar för att dela upp data.

**--Skicka-MD5**  Skapa en MD5-hash av varje fil och spara hashen som Content-MD5-egenskapen för Målmatrisen/-filen. (Som standard skapas inte hashen.) Identiskt med samma-namngivna parameter i kopierings kommandot.

**--storleks per fil** sträng storlek för varje automatiskt genererad data fil. Måste vara ett tal omedelbart följt av K, M eller G. t. ex. 12K eller 200G (standard "250M").

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

**--Cap-Mbit/s float**  CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.

**--Skriv** sträng format för kommandots utdata. Alternativen är: text, JSON. Standardvärdet är ' text '. (standard text).

**--sträng för betrodd-Microsoft-suffix** anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.


## <a name="see-also"></a>Se även

- [AzCopy](storage-ref-azcopy.md)
