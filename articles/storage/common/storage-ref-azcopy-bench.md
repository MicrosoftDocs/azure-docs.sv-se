---
title: azcopycopycopy | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot azcopycopycopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1e49e787854069c2fcea30df7a43c3aacdd21b9e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502036"
---
# <a name="azcopy-benchmark"></a>azcopy benchmark

Kör ett prestandatest genom att ladda upp eller ned testdata till eller från ett angivet mål. För uppladdningar genereras testdata automatiskt.

Benchmark-kommandot kör samma process som "kopiera", förutom att: 

  - I stället för att kräva både käll- och målparametrar tar benchmark bara en. Det här är blobcontainern, Azure Files Resurs eller det Azure Data Lake Storage Gen2 filsystem som du vill ladda upp till eller ladda ned från.

  - Parametern "mode" beskriver om AzCopy ska testa uppladdningar till eller nedladdningar från det angivna målet. Giltiga värden är "Ladda upp" och "Ladda ned". Standardvärdet är "Ladda upp".

  - För uppladdningstestvärden beskrivs nyttolasten av kommandoradsparametrar, som styr hur många filer som genererats automatiskt och hur viktiga filerna är. Genereringsprocessen sker helt i minnet. Disken används inte.

  - För nedladdningar består nyttolasten av de filer som redan finns vid källan. (Se exemplet nedan om hur du genererar testfiler om det behövs).
  
  - Endast några av de valfria parametrar som är tillgängliga för kopieringskommandot stöds.
  
  - Ytterligare diagnostik mäts och rapporteras.
  
  - För uppladdningar är standardbeteendet att ta bort överförda data i slutet av testkörningen.  För nedladdningar sparas data aldrig lokalt.

Prestandatestläget finjusterar sig automatiskt efter antalet parallella TCP-anslutningar som ger maximalt dataflöde. Talet visas i slutet. Om du vill förhindra automatisk AZCOPY_CONCURRENCY_VALUE ange AZCOPY_CONCURRENCY_VALUE miljövariabeln till ett specifikt antal anslutningar. 

Alla vanliga autentiseringstyper stöds. Den en mest praktiska metoden för prestandatestning av uppladdning är dock vanligtvis att skapa en tom container med en SAS-token och använda SAS-autentisering. (Nedladdningsläget kräver att en uppsättning testdata finns i målcontainern.)

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Optimera prestanda för AzCopy v10 med Azure Storage](storage-use-azcopy-optimize.md)


## <a name="examples"></a>Exempel

```azcopy
azcopy benchmark [destination] [flags]
```

Kör ett benchmark-test med standardparametrar (lämpligt för prestandatestning av nätverk upp till 1 Gbit/s):'

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
Kör ett benchmark-test som laddar upp 100 filer, var 2 GiB i storlek: (lämpligt för prestandatestning i ett snabbt nätverk, till exempel 10 Gbit/s):'

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Kör ett benchmark-test men använd 50 000 filer, var och en med 8 MiB i storlek och beräkna deras MD5-hashvärden (på samma sätt som flaggan gör detta i `--put-md5` kopieringskommandot). Syftet med `--put-md5` benchmarking är att testa om MD5-beräkningen påverkar dataflödet för det valda filantalet och den valda storleken:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Kör ett benchmark-test som laddar ned befintliga filer från ett mål

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Kör en uppladdning som inte tar bort de överförda filerna. (Dessa filer kan sedan fungera som nyttolast för ett nedladdningstest)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Alternativ

**--blob-type** string Definierar typen av blob vid målet. Används för att tillåta benchmarking av olika blobtyper. Identisk med samma namngivna parameter i kopieringskommandot (standardvärdet "Identifiera").

**--block-size-mb** float Använd den här blockstorleken (anges i MiB). Standardvärdet beräknas automatiskt baserat på filstorlek. Decimaler tillåts , till exempel 0,25. Identisk med parametern med samma namn i kopieringskommandot.

**--check-length**  Kontrollera längden på en fil på målet efter överföringen. Om det finns ett matchningsfel mellan källa och mål markeras överföringen som misslyckad. (standard true)

**--delete-test-data**  Om det är sant tas benchmark-data bort i slutet av benchmark-körningen.  Ställ in det på false om du vill behålla data vid målet – till exempel för att använda dem för manuella tester utanför benchmark-läget (standardvärdet är true).

**--file-count** uint.  Antalet automatiskt genererade datafiler som ska användas (standard 100).

**--help**  Hjälp för att få hjälp

**--log-level** string (sträng på loggnivå) Definiera loggfilens verbositet, tillgängliga nivåer: INFO(alla begäranden/svar), WARNING(långsamma svar), ERROR(only failed requests) och NONE (inga utdataloggar). (standard "INFO")

**--mode sträng** definierar om Azcopy ska testa uppladdningar eller nedladdningar från det här målet. Giltiga värden är "upload" och "download". Standardalternativet är "upload". (standarduppladdning)

**--number-of-folders** uint Om det är större än 0 skapar du mappar för att dela upp data.

**--put-md5**  Skapa en MD5-hash för varje fil och spara hashen som Content-MD5-egenskapen för målbloben/-filen. (Hashen skapas INTE som standard.) Identisk med parametern med samma namn i kopieringskommandot.

**--size-per-file** string Storlek för varje automatiskt genererad datafil. Måste vara ett tal omedelbart följt av K, M eller G. T.ex. 12k eller 200G (standard "250M").

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

**--cap-mbps float**  Kapslar överföringshastigheten i megabit per sekund. Dataflödet för ögonblick kan variera något från taket. Om det här alternativet är inställt på noll, eller om det utelämnas, är dataflödet inte begränsat.

**--output-type** strängformat för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text". (standard "text").

**--trusted-microsoft-suffixesträng** Anger ytterligare domänsuffix där Azure Active Directory inloggningstoken kan skickas.  Standardvärdet är *.core.windows.net.* core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Alla som visas här läggs till i standardinställningarna. Av säkerhetsskäl bör du bara placera Microsoft Azure här. Avgränsa flera poster med semikolon.


## <a name="see-also"></a>Se även

- [azcopy](storage-ref-azcopy.md)
